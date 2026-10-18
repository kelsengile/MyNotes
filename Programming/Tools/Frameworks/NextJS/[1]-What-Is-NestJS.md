[Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[2]-TypeScript-And-Decorators.md)

*Basics*

# Lesson 1 - What Is NestJS

## 1.1 Why Nest exists

Node.js lets you build servers in JavaScript, and frameworks like Express make it quick to start. But Express is **unopinionated**: it does not tell you where to put files, how to share logic, or how to test your code. On a small project that freedom is fine. On a large project with many developers, every person organizes things differently and the codebase becomes hard to follow.

NestJS solves this by giving you an architecture out of the box:

- **Modules** group related code together.
- **Controllers** receive HTTP requests and return responses.
- **Providers** (usually services) hold business logic and can be shared.
- **Dependency injection (DI)** lets Nest create and connect these pieces for you.

Nest is written in TypeScript and heavily inspired by Angular, so if you have seen Angular's modules, decorators, and services, Nest will feel familiar.

---

## 1.2 Nest vs Express

Nest does not replace Express. By default, Nest **runs on top of Express** and can optionally run on Fastify. You still get all the HTTP power of those libraries, but with structure added.

| Aspect | Express | NestJS |
|---|---|---|
| Structure | You decide | Enforced by modules, controllers, providers |
| Language | JavaScript (TypeScript optional) | TypeScript first |
| Dependency injection | Manual | Built in |
| Validation, auth, docs | Third-party, hand-wired | Official integrations |
| Learning curve | Low | Medium |

A simple Express route:

```ts
import express from 'express';

const app = express();

app.get('/cats', (req, res) => {
  res.json([{ name: 'Tom' }]);
});

app.listen(3000);
```

The same idea in Nest:

```ts
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll() {
    return [{ name: 'Tom' }];
  }
}
```

Notice that in Nest you simply **return** a value. Nest converts it to a JSON response for you.

---

## 1.3 Installing the Nest CLI

**Requirements:** a currently supported version of [Node.js](https://nodejs.org) (check the Nest docs for the minimum) and npm.

Install the CLI globally:

```bash
npm install -g @nestjs/cli
```

Create a new project:

```bash
nest new my-nest-app
```

The CLI asks which package manager to use, then creates the project and installs dependencies. Start the app in watch mode (it restarts when you save a file):

```bash
cd my-nest-app
npm run start:dev
```

Open `http://localhost:3000` in your browser. You should see `Hello World!`.

The CLI can also generate code for you. `nest generate` can be shortened to `nest g`:

```bash
nest g module users        # creates users.module.ts
nest g controller users    # creates users.controller.ts
nest g service users       # creates users.service.ts
nest g resource users      # creates module, controller, service, DTOs, entity
```

Add `--no-spec` to skip generating test files.

---

## 1.4 Tour of a generated project

```
my-nest-app/
├── src/
│   ├── app.controller.ts
│   ├── app.controller.spec.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── test/
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── nest-cli.json
├── package.json
├── tsconfig.json
└── tsconfig.build.json
```

| File | Purpose |
|---|---|
| `main.ts` | Entry point. Creates the app and starts listening. |
| `app.module.ts` | The root module. Every app has exactly one. |
| `app.controller.ts` | A sample controller with one route. |
| `app.service.ts` | A sample provider used by the controller. |
| `*.spec.ts` | Unit tests (Jest). |
| `test/` | End-to-end tests. |
| `nest-cli.json` | Nest CLI configuration. |

The entry point:

```ts
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

`NestFactory.create(AppModule)` builds the application starting from the root module, wires up all dependencies, and returns an app object. `app.listen()` starts the HTTP server.

The root module ties the sample pieces together:

```ts
// src/app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

You will learn what each part of `@Module()` means in Lesson 3.

---

[Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[2]-TypeScript-And-Decorators.md)