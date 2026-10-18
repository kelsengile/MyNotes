[Previous](./[19]-WebSockets-And-Real-Time.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[21]-Testing-NestJS-Applications.md)

*Advanced Features*

# Lesson 20 - Microservices And Queues

## 20.1 Transport layers

A **microservice architecture** splits an application into small services that communicate with each other. In Nest, a microservice is an app that uses a **transport layer** other than HTTP to receive messages.

```bash
npm install @nestjs/microservices
```

Built-in transports include:

| Transport | Typical use |
|---|---|
| `TCP` | Simple, fast service-to-service calls, good for learning |
| `Redis` | Lightweight pub/sub messaging |
| `NATS` | High-performance messaging |
| `RabbitMQ` (`RMQ`) | Reliable message queues with acknowledgements |
| `Kafka` | High-volume event streaming |
| `gRPC` | Strongly typed RPC using Protocol Buffers |

Create a TCP microservice (no HTTP at all):

```ts
// main.ts of the "users" service
import { NestFactory } from '@nestjs/core';
import { MicroserviceOptions, Transport } from '@nestjs/microservices';

async function bootstrap() {
  const app = await NestFactory.createMicroservice<MicroserviceOptions>(AppModule, {
    transport: Transport.TCP,
    options: { host: '127.0.0.1', port: 4001 },
  });
  await app.listen();
}
bootstrap();
```

A **hybrid app** can serve HTTP and microservice traffic together with `app.connectMicroservice()` followed by `app.startAllMicroservices()`.

---

## 20.2 Message and event patterns

There are two styles of communication:

| Style | Decorator | Behavior |
|---|---|---|
| **Request-response** | `@MessagePattern()` | The caller waits for a reply |
| **Event-based** | `@EventPattern()` | Fire and forget; no reply expected |

The receiving service:

```ts
import { Controller } from '@nestjs/common';
import { EventPattern, MessagePattern, Payload } from '@nestjs/microservices';

@Controller()
export class UsersController {
  @MessagePattern({ cmd: 'get_user' })
  getUser(@Payload() data: { id: number }) {
    return { id: data.id, name: 'Ada' }; // reply
  }

  @EventPattern('user_created')
  handleUserCreated(@Payload() data: { id: number }) {
    console.log('A user was created', data.id); // no reply
  }
}
```

The calling service registers a **client** and uses it to send:

```ts
// module
import { ClientsModule, Transport } from '@nestjs/microservices';

@Module({
  imports: [
    ClientsModule.register([
      { name: 'USERS_SERVICE', transport: Transport.TCP, options: { host: '127.0.0.1', port: 4001 } },
    ]),
  ],
  controllers: [GatewayController],
})
export class GatewayModule {}
```

```ts
import { ClientProxy } from '@nestjs/microservices';
import { firstValueFrom } from 'rxjs';

@Controller('users')
export class GatewayController {
  constructor(@Inject('USERS_SERVICE') private readonly client: ClientProxy) {}

  @Get(':id')
  getUser(@Param('id', ParseIntPipe) id: number) {
    // send(): request-response, returns an Observable
    return firstValueFrom(this.client.send({ cmd: 'get_user' }, { id }));
  }

  @Post()
  create() {
    // emit(): event, no reply
    this.client.emit('user_created', { id: 1 });
    return { ok: true };
  }
}
```

Handle failures: remote calls can time out or fail, so use RxJS `timeout()` and `catchError()` and map errors to proper HTTP exceptions.

---

## 20.3 Queues with Bull

A **queue** lets you do slow work later, in the background, instead of during the request (sending emails, resizing images, generating reports). The request adds a **job** to the queue and returns immediately; a **worker** processes it.

The Nest ecosystem uses Redis-backed queues. **BullMQ** is the modern successor to the original **Bull** library and is the recommended choice for new projects (`@nestjs/bull` for the legacy library is still available). The concepts below are the same for both.

```bash
npm install @nestjs/bullmq bullmq
```

You need a running Redis server.

```ts
import { BullModule } from '@nestjs/bullmq';

@Module({
  imports: [
    BullModule.forRoot({ connection: { host: 'localhost', port: 6379 } }),
    BullModule.registerQueue({ name: 'emails' }),
  ],
  providers: [EmailsProducer, EmailsProcessor],
})
export class EmailsModule {}
```

**Producer:** adds jobs.

```ts
import { InjectQueue } from '@nestjs/bullmq';
import { Queue } from 'bullmq';

@Injectable()
export class EmailsProducer {
  constructor(@InjectQueue('emails') private readonly queue: Queue) {}

  async sendWelcome(userId: number, email: string) {
    await this.queue.add(
      'welcome',
      { userId, email },
      { attempts: 3, backoff: { type: 'exponential', delay: 2000 }, removeOnComplete: true },
    );
  }
}
```

**Processor:** the worker that runs jobs.

```ts
import { Processor, WorkerHost } from '@nestjs/bullmq';
import { Job } from 'bullmq';

@Processor('emails')
export class EmailsProcessor extends WorkerHost {
  async process(job: Job<{ userId: number; email: string }>) {
    switch (job.name) {
      case 'welcome':
        // send the email here
        console.log(`Sending welcome email to ${job.data.email}`);
        return { sent: true };
    }
  }
}
```

Useful job options: `delay` (run later), `attempts` and `backoff` (retry on failure), `priority`, and `repeat` (recurring jobs). Make jobs **idempotent** (safe to run twice), because retries can cause duplicates.

---

## 20.4 Scheduling and caching

### Scheduling

Run code at set times with `@nestjs/schedule`:

```bash
npm install @nestjs/schedule
```

```ts
import { ScheduleModule } from '@nestjs/schedule';

@Module({ imports: [ScheduleModule.forRoot()] })
export class AppModule {}
```

```ts
import { Cron, CronExpression, Interval, Timeout } from '@nestjs/schedule';

@Injectable()
export class TasksService {
  @Cron(CronExpression.EVERY_DAY_AT_MIDNIGHT)
  cleanup() {
    console.log('Nightly cleanup');
  }

  @Interval(60_000)       // every 60 seconds
  heartbeat() {}

  @Timeout(5_000)         // once, 5 seconds after startup
  startupTask() {}
}
```

If you run several instances of your app, every instance runs the schedule. Use a queue with a repeatable job, or a lock, so the work runs only once.

### Caching

Caching stores results of expensive operations so repeated requests are fast.

```bash
npm install @nestjs/cache-manager cache-manager
```

```ts
import { CacheModule } from '@nestjs/cache-manager';

@Module({
  imports: [CacheModule.register({ isGlobal: true, ttl: 30_000 })], // ttl in ms in current versions
})
export class AppModule {}
```

Automatic caching of GET responses:

```ts
import { CacheInterceptor, CacheKey, CacheTTL } from '@nestjs/cache-manager';

@Controller('cats')
@UseInterceptors(CacheInterceptor)
export class CatsController {
  @Get()
  @CacheTTL(10_000)
  findAll() {
    return this.catsService.findAll();
  }
}
```

Manual caching in a service:

```ts
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { Cache } from 'cache-manager';

constructor(@Inject(CACHE_MANAGER) private cache: Cache) {}

async getStats() {
  const cached = await this.cache.get('stats');
  if (cached) return cached;

  const stats = await this.computeStats();
  await this.cache.set('stats', stats, 60_000);
  return stats;
}
```

The default store is in memory (per instance). For multiple instances, use a shared store such as Redis. Always decide how cached data is **invalidated** when the underlying data changes. TTL units have changed between `cache-manager` versions (seconds vs milliseconds), so check the docs for your installed version.

---

[Previous](./[19]-WebSockets-And-Real-Time.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[21]-Testing-NestJS-Applications.md)