[Previous](./[12]-Databases-With-TypeORM.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[14]-Authentication-With-Passport-And-JWT.md)

*Data And Configuration*

# Lesson 13 - Prisma And Mongoose

## 13.1 Using Prisma in Nest

**Prisma** is a type-safe database toolkit. You describe your data in a `schema.prisma` file, and Prisma generates a fully typed client.

> **Note:** Prisma's client setup (generator options, driver adapters) has changed between major versions. The steps below show the classic setup; always check the [official Prisma docs](https://www.prisma.io/docs) for the version you install.

```bash
npm install prisma --save-dev
npm install @prisma/client
npx prisma init
```

Define models in `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String
  posts Post[]
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  author   User   @relation(fields: [authorId], references: [id])
  authorId Int
}
```

Create tables and generate the client:

```bash
npx prisma migrate dev --name init
npx prisma generate
```

Wrap the client in an injectable Nest service:

```ts
// src/prisma/prisma.service.ts
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    await this.$connect();
  }

  async onModuleDestroy() {
    await this.$disconnect();
  }
}
```

```ts
// src/prisma/prisma.module.ts
import { Global, Module } from '@nestjs/common';

@Global()
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}
```

Use it in a service:

```ts
@Injectable()
export class UsersService {
  constructor(private readonly prisma: PrismaService) {}

  create(data: { email: string; name: string }) {
    return this.prisma.user.create({ data });
  }

  findAll() {
    return this.prisma.user.findMany({ include: { posts: true } });
  }

  findOne(id: number) {
    return this.prisma.user.findUnique({ where: { id } });
  }
}
```

---

## 13.2 Using Mongoose for MongoDB

**Mongoose** is the popular library for MongoDB, a document database.

```bash
npm install @nestjs/mongoose mongoose
```

Connect in the root module:

```ts
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [MongooseModule.forRoot('mongodb://localhost:27017/mydb')],
})
export class AppModule {}
```

Define a schema using decorators:

```ts
// src/cats/schemas/cat.schema.ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';

export type CatDocument = HydratedDocument<Cat>;

@Schema({ timestamps: true })
export class Cat {
  @Prop({ required: true })
  name: string;

  @Prop()
  age: number;

  @Prop([String])
  tags: string[];
}

export const CatSchema = SchemaFactory.createForClass(Cat);
```

Register the schema in the feature module and inject the model:

```ts
@Module({
  imports: [MongooseModule.forFeature([{ name: Cat.name, schema: CatSchema }])],
  providers: [CatsService],
  controllers: [CatsController],
})
export class CatsModule {}
```

```ts
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';

@Injectable()
export class CatsService {
  constructor(@InjectModel(Cat.name) private readonly catModel: Model<Cat>) {}

  create(dto: CreateCatDto) {
    return this.catModel.create(dto);
  }

  findAll() {
    return this.catModel.find().exec();
  }

  findOne(id: string) {
    return this.catModel.findById(id).exec();
  }
}
```

---

## 13.3 Choosing a data layer

| Tool | Database type | Strengths | Trade-offs |
|---|---|---|---|
| **TypeORM** | SQL | Decorator-based entities, fits Nest style, query builder | Some advanced typing gaps; migrations take care |
| **Prisma** | SQL (and MongoDB support) | Excellent type safety and developer experience, great migrations | Separate schema language; generated client step |
| **Mongoose** | MongoDB | Mature, flexible documents, schema validation | Only for MongoDB; relations are manual |

Guidance for beginners:

- **Relational data** (users, orders, payments with clear relationships): choose SQL with **Prisma** or **TypeORM**.
- **Flexible, document-shaped data** (content, logs, varied attributes): **Mongoose** with MongoDB.
- Pick **one** data layer per service where possible, and keep it hidden behind your services so you could swap it later.

---

## 13.4 Testing data access

Unit tests should not need a real database. **Mock** the data layer:

```ts
const prismaMock = {
  user: {
    findMany: jest.fn().mockResolvedValue([{ id: 1, email: 'a@b.com', name: 'A' }]),
    create: jest.fn(),
  },
};

const moduleRef = await Test.createTestingModule({
  providers: [UsersService, { provide: PrismaService, useValue: prismaMock }],
}).compile();

const service = moduleRef.get(UsersService);
expect(await service.findAll()).toHaveLength(1);
expect(prismaMock.user.findMany).toHaveBeenCalled();
```

For Mongoose, provide the model token with `getModelToken`:

```ts
import { getModelToken } from '@nestjs/mongoose';

providers: [CatsService, { provide: getModelToken(Cat.name), useValue: catModelMock }]
```

For TypeORM, use `getRepositoryToken(User)` from `@nestjs/typeorm`.

For **integration/e2e tests** that verify real queries, use a dedicated test database (a Docker container, or SQLite for simple cases), run migrations before the tests, and clean the data between tests. Never point tests at your development or production database.

---

[Previous](./[12]-Databases-With-TypeORM.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[14]-Authentication-With-Passport-And-JWT.md)