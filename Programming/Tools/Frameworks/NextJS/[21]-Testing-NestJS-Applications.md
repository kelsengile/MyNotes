[Previous](./[20]-Microservices-And-Queues.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[22]-Deployment.md)

*Quality And Delivery*

# Lesson 21 - Testing NestJS Applications

## 21.1 Unit testing with Jest

**Jest** is the test runner configured by default in new Nest projects. A **unit test** checks one piece of code (such as a service) in isolation, without a database or network.

Run tests:

```bash
npm run test          # run once
npm run test:watch    # re-run on changes
npm run test:cov      # coverage report
npm run test:e2e      # end-to-end tests
```

A plain Jest test with the **Arrange / Act / Assert** structure:

```ts
describe('math', () => {
  it('adds numbers', () => {
    const a = 2;              // Arrange
    const result = a + 3;     // Act
    expect(result).toBe(5);   // Assert
  });
});
```

Useful Jest tools:

| Tool | Use |
|---|---|
| `describe` / `it` | Group and define tests |
| `expect(x).toBe(y)` | Strict equality (primitives) |
| `expect(x).toEqual(y)` | Deep equality (objects/arrays) |
| `expect(fn).toThrow()` | Expect an error |
| `await expect(promise).rejects.toThrow()` | Expect an async error |
| `jest.fn()` | Create a mock function |
| `beforeEach` | Run setup before every test |

Test files are named `*.spec.ts` and live next to the code they test.

---

## 21.2 The testing module

`Test.createTestingModule()` builds a small Nest module just for the test, with real DI. You choose which providers to include and which to replace with fakes.

```ts
import { Test, TestingModule } from '@nestjs/testing';
import { NotFoundException } from '@nestjs/common';

describe('UsersService', () => {
  let service: UsersService;

  const repoMock = {
    find: jest.fn(),
    findOneBy: jest.fn(),
    save: jest.fn(),
    create: jest.fn((dto) => dto),
  };

  beforeEach(async () => {
    const moduleRef: TestingModule = await Test.createTestingModule({
      providers: [
        UsersService,
        { provide: getRepositoryToken(User), useValue: repoMock },
      ],
    }).compile();

    service = moduleRef.get(UsersService);
    jest.clearAllMocks();
  });

  it('returns all users', async () => {
    repoMock.find.mockResolvedValue([{ id: 1, name: 'Ada' }]);

    const result = await service.findAll();

    expect(result).toHaveLength(1);
    expect(repoMock.find).toHaveBeenCalledTimes(1);
  });

  it('throws when a user is missing', async () => {
    repoMock.findOneBy.mockResolvedValue(null);

    await expect(service.findOne(99)).rejects.toThrow(NotFoundException);
  });
});
```

Testing a controller works the same way: provide the controller and a mocked service.

```ts
const moduleRef = await Test.createTestingModule({
  controllers: [UsersController],
  providers: [{ provide: UsersService, useValue: { findAll: jest.fn().mockReturnValue([]) } }],
}).compile();

const controller = moduleRef.get(UsersController);
expect(controller.findAll()).toEqual([]);
```

For request-scoped or transient providers, use `await moduleRef.resolve(Provider)` instead of `get()`.

---

## 21.3 End-to-end testing

An **end-to-end (e2e) test** starts the real app (or most of it) and makes real HTTP requests using **Supertest**. It verifies that routing, pipes, guards, and filters all work together.

```ts
// test/users.e2e-spec.ts
import { INestApplication, ValidationPipe } from '@nestjs/common';
import { Test } from '@nestjs/testing';
import * as request from 'supertest';
import { AppModule } from '../src/app.module';

describe('Users (e2e)', () => {
  let app: INestApplication;

  beforeAll(async () => {
    const moduleRef = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleRef.createNestApplication();
    // Apply the SAME global setup as main.ts, or tests will not match reality
    app.useGlobalPipes(new ValidationPipe({ whitelist: true, transform: true }));
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  it('POST /users creates a user', () => {
    return request(app.getHttpServer())
      .post('/users')
      .send({ name: 'Ada', email: 'ada@example.com' })
      .expect(201)
      .expect((res) => {
        expect(res.body).toMatchObject({ name: 'Ada' });
      });
  });

  it('POST /users rejects invalid email', () => {
    return request(app.getHttpServer())
      .post('/users')
      .send({ name: 'Ada', email: 'not-an-email' })
      .expect(400);
  });
});
```

Depending on your `esModuleInterop` setting and Supertest version, the import may need to be `import request from 'supertest'` instead. Check your project's TypeScript configuration.

Tips:

- Use a **separate test database** and clean it between tests.
- Keep e2e tests for important flows; unit tests handle most logic because they are faster.
- Always close the app in `afterAll`, or Jest may hang.

---

## 21.4 Mocking providers

You can replace providers in two ways.

**1. Provide a fake directly (shown above):**

```ts
{ provide: MailService, useValue: { send: jest.fn() } }
```

**2. Override a provider in an existing module** (useful in e2e tests that import the real `AppModule`):

```ts
const moduleRef = await Test.createTestingModule({ imports: [AppModule] })
  .overrideProvider(MailService)
  .useValue({ send: jest.fn().mockResolvedValue(true) })
  .overrideGuard(JwtAuthGuard)
  .useValue({ canActivate: () => true })
  .compile();
```

`overrideGuard`, `overrideInterceptor`, `overridePipe`, and `overrideFilter` exist as well. Overriding the auth guard is a common way to test routes without generating real tokens.

Automatic mocks: `useMocker` creates mocks for any missing dependencies so you only define the ones you care about.

```ts
const moduleRef = await Test.createTestingModule({ providers: [UsersService] })
  .useMocker((token) => {
    if (token === MailService) {
      return { send: jest.fn() };
    }
    return {}; // empty mock for everything else
  })
  .compile();
```

Good testing habits:

- Test **behavior**, not implementation details.
- Mock **boundaries** (database, network, email), not the code under test.
- Keep tests independent: reset mocks with `jest.clearAllMocks()` and avoid shared state.
- Write a test for each bug you fix.
- Aim for meaningful coverage of business logic rather than chasing 100%.

---

[Previous](./[20]-Microservices-And-Queues.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[22]-Deployment.md)