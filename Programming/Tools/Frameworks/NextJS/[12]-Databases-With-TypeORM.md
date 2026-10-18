[Previous](./[11]-Configuration-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[13]-Prisma-And-Mongoose.md)

*Data And Configuration*

# Lesson 12 - Databases With TypeORM

## 12.1 Entities and repositories

**TypeORM** is an ORM (Object-Relational Mapper): you describe tables as classes and work with objects instead of writing SQL for everything.

Install the packages (PostgreSQL shown; use `mysql2` or `sqlite3` for others):

```bash
npm install @nestjs/typeorm typeorm pg
```

Connect in the root module, reading settings from config:

```ts
import { TypeOrmModule } from '@nestjs/typeorm';
import { ConfigService } from '@nestjs/config';

TypeOrmModule.forRootAsync({
  inject: [ConfigService],
  useFactory: (config: ConfigService) => ({
    type: 'postgres',
    url: config.getOrThrow<string>('DATABASE_URL'),
    autoLoadEntities: true,                          // entities registered via forFeature are loaded
    synchronize: config.get('NODE_ENV') !== 'production', // development only!
  }),
})
```

An **entity** is a class mapped to a table:

```ts
import { Column, CreateDateColumn, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity('users')
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ unique: true })
  email: string;

  @Column()
  name: string;

  @Column({ default: true })
  isActive: boolean;

  @CreateDateColumn()
  createdAt: Date;
}
```

Register entities per feature module and inject a **repository**:

```ts
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  providers: [UsersService],
  controllers: [UsersController],
})
export class UsersModule {}
```

```ts
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';

@Injectable()
export class UsersService {
  constructor(@InjectRepository(User) private readonly users: Repository<User>) {}

  create(dto: CreateUserDto) {
    return this.users.save(this.users.create(dto));
  }

  findAll() {
    return this.users.find();
  }

  findOne(id: number) {
    return this.users.findOneBy({ id });
  }

  async remove(id: number) {
    await this.users.delete(id);
  }
}
```

Repository methods you will use often: `find`, `findOneBy`, `findOne`, `save`, `create`, `update`, `delete`, `count`, and `createQueryBuilder` for complex queries.

---

## 12.2 Relations

Relations connect tables.

```ts
import { Entity, PrimaryGeneratedColumn, Column, OneToMany, ManyToOne, ManyToMany, JoinTable } from 'typeorm';

@Entity()
export class User {
  @PrimaryGeneratedColumn() id: number;
  @Column() name: string;

  @OneToMany(() => Post, (post) => post.author)
  posts: Post[];
}

@Entity()
export class Post {
  @PrimaryGeneratedColumn() id: number;
  @Column() title: string;

  @ManyToOne(() => User, (user) => user.posts, { onDelete: 'CASCADE' })
  author: User;

  @ManyToMany(() => Tag, (tag) => tag.posts)
  @JoinTable() // put on ONE side of a many-to-many
  tags: Tag[];
}

@Entity()
export class Tag {
  @PrimaryGeneratedColumn() id: number;
  @Column() label: string;

  @ManyToMany(() => Post, (post) => post.tags)
  posts: Post[];
}
```

| Decorator | Meaning |
|---|---|
| `@OneToOne` | One row matches one row (add `@JoinColumn()` on the owning side) |
| `@OneToMany` / `@ManyToOne` | One parent, many children (the "many" side holds the foreign key) |
| `@ManyToMany` | Both sides have many; needs `@JoinTable()` on one side |

Relations are **not loaded by default**. Ask for them explicitly:

```ts
this.users.find({ relations: { posts: true } });
this.users.findOne({ where: { id }, relations: { posts: { tags: true } } });
```

---

## 12.3 Migrations

`synchronize: true` alters your database automatically to match entities. It is convenient in development but can **destroy data in production**. In production use **migrations**: versioned scripts that change the schema safely.

Create a data source file for the TypeORM CLI:

```ts
// src/data-source.ts
import 'dotenv/config';
import { DataSource } from 'typeorm';

export default new DataSource({
  type: 'postgres',
  url: process.env.DATABASE_URL,
  entities: ['src/**/*.entity.ts'],
  migrations: ['src/migrations/*.ts'],
});
```

Add scripts to `package.json`:

```json
{
  "scripts": {
    "typeorm": "typeorm-ts-node-commonjs -d src/data-source.ts",
    "migration:generate": "npm run typeorm -- migration:generate",
    "migration:run": "npm run typeorm -- migration:run",
    "migration:revert": "npm run typeorm -- migration:revert"
  }
}
```

Workflow:

```bash
# 1. Change an entity, then generate a migration from the difference
npm run migration:generate -- src/migrations/AddUserNickname

# 2. Review the generated file, then apply it
npm run migration:run

# 3. Undo the last migration if needed
npm run migration:revert
```

Always **review** generated migrations before running them, commit them to git, and set `synchronize: false` in production. Name your entity files `*.entity.ts` so the glob above finds them.

---

## 12.4 Transactions

A **transaction** groups several database operations so they either **all succeed or all fail**. Use one when changes must stay consistent, such as moving money between accounts.

The simplest approach uses `DataSource.transaction`:

```ts
import { DataSource } from 'typeorm';

@Injectable()
export class TransferService {
  constructor(private readonly dataSource: DataSource) {}

  async transfer(fromId: number, toId: number, amount: number) {
    await this.dataSource.transaction(async (manager) => {
      const from = await manager.findOneByOrFail(Account, { id: fromId });
      const to = await manager.findOneByOrFail(Account, { id: toId });

      if (from.balance < amount) {
        throw new BadRequestException('Insufficient funds'); // triggers rollback
      }

      from.balance -= amount;
      to.balance += amount;

      await manager.save([from, to]);
    });
  }
}
```

If any error is thrown inside the callback, everything is rolled back. Use the provided `manager` for **all** queries in the transaction, not your normal repositories, otherwise those queries run outside it.

For finer control, use a `QueryRunner`:

```ts
const queryRunner = this.dataSource.createQueryRunner();
await queryRunner.connect();
await queryRunner.startTransaction();
try {
  await queryRunner.manager.save(entityA);
  await queryRunner.manager.save(entityB);
  await queryRunner.commitTransaction();
} catch (err) {
  await queryRunner.rollbackTransaction();
  throw err;
} finally {
  await queryRunner.release(); // always release the connection
}
```

---

[Previous](./[11]-Configuration-And-Environments.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[13]-Prisma-And-Mongoose.md)