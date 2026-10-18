[Previous](./[15]-Authorization-And-Roles.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[17]-OpenAPI-And-Documentation.md)

*Security*

# Lesson 16 - API Security

## 16.1 CORS and Helmet

### CORS

Browsers block a web page from calling an API on a different origin (domain, port, or protocol) unless the API allows it using **CORS (Cross-Origin Resource Sharing)** headers. Enable and restrict it in `main.ts`:

```ts
app.enableCors({
  origin: ['https://myapp.com'], // list allowed origins; avoid '*' for authenticated APIs
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  credentials: true,             // allow cookies/authorization headers
});
```

CORS protects **browsers only**. It does not stop tools like curl or other servers, so you still need authentication.

### Helmet

**Helmet** sets protective HTTP headers (such as `X-Content-Type-Options` and `Strict-Transport-Security`) with one line.

```bash
npm install helmet
```

```ts
import helmet from 'helmet';

app.use(helmet());
```

If you serve Swagger UI (Lesson 17), Helmet's default Content Security Policy may need adjusting for that route.

---

## 16.2 Rate limiting

Rate limiting caps how many requests a client can make, which slows down brute-force logins and abusive scraping.

```bash
npm install @nestjs/throttler
```

```ts
import { ThrottlerModule, ThrottlerGuard } from '@nestjs/throttler';
import { APP_GUARD } from '@nestjs/core';

@Module({
  imports: [
    ThrottlerModule.forRoot([{ ttl: 60000, limit: 100 }]), // 100 requests per minute (ttl is in ms in current versions)
  ],
  providers: [{ provide: APP_GUARD, useClass: ThrottlerGuard }],
})
export class AppModule {}
```

Override the limit for sensitive routes, or skip it for others:

```ts
import { Throttle, SkipThrottle } from '@nestjs/throttler';

@Throttle({ default: { limit: 5, ttl: 60000 } }) // 5 attempts per minute
@Post('login')
login() {}

@SkipThrottle()
@Get('health')
health() {}
```

Clients that exceed the limit receive `429 Too Many Requests`. If your app is behind a proxy or load balancer, configure Express's `trust proxy` setting so the real client IP is used. For multiple server instances, use a shared storage (such as Redis) for counters. Check the throttler docs for the storage option and for the exact option names of your installed version.

---

## 16.3 Input sanitization

**Never trust client input.** Layers of defense:

1. **Validate** all input with DTOs and a global `ValidationPipe` using `whitelist: true` (Lesson 7). This blocks unexpected fields and wrong types.
2. **Use parameterized queries.** ORMs like TypeORM and Prisma do this for you. Never build SQL by concatenating user input:

```ts
// DANGEROUS: SQL injection
this.dataSource.query(`SELECT * FROM users WHERE email = '${email}'`);

// SAFE: parameters
this.dataSource.query('SELECT * FROM users WHERE email = $1', [email]);
```

3. **Escape or sanitize text** that will later be shown as HTML, to prevent cross-site scripting (XSS). The best defense is to escape on output in your frontend; if you must clean stored HTML, use a vetted library such as `sanitize-html`.
4. **Limit sizes.** Cap request body size and string lengths:

```ts
@MaxLength(500)
bio: string;
```

```ts
import { json } from 'express';
app.use(json({ limit: '1mb' }));
```

5. **Check file uploads** for type and size, and never trust the file name sent by the client.
6. **Do not mass-assign** request bodies directly into database entities. Always go through a DTO.

---

## 16.4 Secure configuration

A checklist for a safe production setup:

| Area | Practice |
|---|---|
| Transport | Serve only over HTTPS; redirect HTTP; enable HSTS (Helmet does) |
| Secrets | Load from environment/secret manager, never commit them (Lesson 11) |
| Passwords | Hash with bcrypt or argon2, never store plain text |
| Errors | Do not leak stack traces or internal errors to clients (Lesson 9) |
| Dependencies | Run `npm audit` regularly and keep packages updated |
| Docs | Disable or protect Swagger UI in production if the API is private |
| Debugging | Turn off verbose logging of request bodies containing personal data |
| Database | Use a least-privilege database user; never connect as superuser |
| Headers | Hide framework fingerprint: `app.disable('x-powered-by')` (Helmet also removes it) |
| Auth | Short token lifetimes, refresh-token rotation (Lesson 14) |

A typical hardened `main.ts`:

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.use(helmet());
  app.enableCors({ origin: process.env.CORS_ORIGIN?.split(','), credentials: true });
  app.useGlobalPipes(new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true, transform: true }));
  app.enableShutdownHooks();

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

Security is layered: no single tool is enough, so combine validation, authentication, authorization, rate limiting, and safe configuration.

---

[Previous](./[15]-Authorization-And-Roles.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[17]-OpenAPI-And-Documentation.md)