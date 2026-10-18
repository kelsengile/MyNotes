[Previous](./[22]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md)

*Quality And Delivery*

# Lesson 23 - Next Steps With NestJS

## 23.1 Monorepo mode

A **monorepo** keeps several apps and shared libraries in one repository. Nest supports this directly in the CLI, which is useful when you have, for example, an API, a worker, and shared code.

Convert an existing project, or add a second app:

```bash
nest generate app worker      # adds apps/worker and converts the project to monorepo mode
nest generate library common  # adds libs/common
```

Resulting structure:

```
my-project/
├── apps/
│   ├── my-project/     # original app
│   └── worker/         # second app
├── libs/
│   └── common/         # shared code
├── nest-cli.json
└── tsconfig.json
```

Import a library using its path alias (configured automatically in `tsconfig.json`):

```ts
import { CommonModule } from '@app/common';
```

Run and build one app:

```bash
nest start worker --watch
nest build worker
```

Good candidates for shared libraries: DTOs and types, authentication helpers, logging setup, database entities, and configuration. Keep libraries focused, and avoid circular dependencies between apps and libs.

---

## 23.2 CQRS and DDD patterns

As an application grows, the single "service does everything" style can become tangled. Two popular patterns help.

### CQRS (Command Query Responsibility Segregation)

CQRS separates **commands** (actions that change state) from **queries** (reads that return data). Nest provides `@nestjs/cqrs`.

```bash
npm install @nestjs/cqrs
```

```ts
// command
export class CreateOrderCommand {
  constructor(public readonly userId: number, public readonly items: string[]) {}
}

// handler
import { CommandHandler, ICommandHandler, EventBus } from '@nestjs/cqrs';

@CommandHandler(CreateOrderCommand)
export class CreateOrderHandler implements ICommandHandler<CreateOrderCommand> {
  constructor(private readonly eventBus: EventBus) {}

  async execute(command: CreateOrderCommand) {
    // save order...
    this.eventBus.publish(new OrderCreatedEvent(1));
    return { id: 1 };
  }
}
```

```ts
// controller sends the command through the bus
import { CommandBus } from '@nestjs/cqrs';

@Post()
create(@Body() dto: CreateOrderDto) {
  return this.commandBus.execute(new CreateOrderCommand(dto.userId, dto.items));
}
```

Register handlers in the module's `providers` and import `CqrsModule`. Queries work the same way with `QueryBus`, `@QueryHandler`, and `IQueryHandler`; **events** use `@EventsHandler` so other parts of the system can react to something that happened.

### DDD (Domain-Driven Design)

DDD organizes code around the **business domain**: use the language of the business, keep business rules in domain objects (entities, value objects, aggregates) rather than in controllers, and separate layers:

- **Domain:** pure business rules, no framework imports
- **Application:** use cases that coordinate the domain (commands, queries, handlers)
- **Infrastructure:** databases, queues, external APIs
- **Presentation:** controllers, resolvers, gateways

These patterns add structure and files. Use them when the domain is complex, not for a simple CRUD app.

---

## 23.3 Performance tuning

Measure first, then optimize. Common wins:

| Area | Action |
|---|---|
| HTTP adapter | Use **Fastify** instead of Express for higher throughput (`@nestjs/platform-fastify`) |
| Database | Add indexes, fetch only needed columns, avoid N+1 queries, paginate lists |
| Caching | Cache expensive reads (Lesson 20.4), with a shared store like Redis |
| Scopes | Avoid request-scoped providers unless required (Lesson 5.3) |
| Heavy work | Move it to queues and workers (Lesson 20.3) |
| Payloads | Enable compression, return only needed fields, paginate |
| Startup | Avoid doing heavy work in constructors; use lifecycle hooks properly |
| Scaling | Run multiple instances behind a load balancer; keep the app stateless |

Switching to Fastify:

```bash
npm install @nestjs/platform-fastify
```

```ts
import { NestFactory } from '@nestjs/core';
import { FastifyAdapter, NestFastifyApplication } from '@nestjs/platform-fastify';

const app = await NestFactory.create<NestFastifyApplication>(AppModule, new FastifyAdapter());
await app.listen(3000, '0.0.0.0');
```

Fastify uses a different request/response API from Express, so Express-specific middleware and `@Res()` usage may need changes.

Pagination example:

```ts
async findPage(page = 1, limit = 20) {
  const [items, total] = await this.repo.findAndCount({
    skip: (page - 1) * limit,
    take: Math.min(limit, 100), // cap the page size
    order: { id: 'ASC' },
  });
  return { items, total, page, pages: Math.ceil(total / limit) };
}
```

Tools for finding bottlenecks: Node's built-in `--inspect` and `--prof`, Clinic.js, load testers such as `autocannon` or `k6`, and your database's slow query log. Always compare before and after.

---

## 23.4 Project ideas

The best way to learn is to build. Ideas in rough order of difficulty:

1. **Todo API:** CRUD, DTO validation, an in-memory or SQLite store. (Lessons 3 to 7)
2. **Blog API:** users, posts, comments with relations, pagination, and JWT auth. (Lessons 12 to 15)
3. **URL shortener:** unique codes, redirects, click counting, rate limiting, caching. (Lessons 16, 20)
4. **Notes app with roles:** admins and users, ownership rules, and Swagger docs. (Lessons 15, 17)
5. **Real-time chat:** rooms, authenticated WebSockets, message history in a database. (Lessons 12, 19)
6. **E-commerce backend:** products, carts, orders in transactions, background emails via queues. (Lessons 12, 20)
7. **GraphQL social feed:** posts, follows, dataloaders to avoid N+1 queries. (Lesson 18)
8. **Microservices demo:** an API gateway plus users and orders services, communicating by messages. (Lesson 20)
9. **Production-ready template:** config validation, health checks, Docker, CI, tests, and logging, ready to reuse for future projects. (Lessons 11, 21, 22)

Tips for finishing projects:

- Start small and ship a working version before adding features.
- Write tests for the important behavior as you go.
- Keep a README that explains how to run the project.
- Read the [official NestJS documentation](https://docs.nestjs.com), which is the best reference as the framework evolves.

Congratulations on finishing the NestJS Topic!

---

[Previous](./[22]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md)