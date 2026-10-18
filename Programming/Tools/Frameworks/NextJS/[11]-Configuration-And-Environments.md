[Previous](./[10]-Request-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[12]-Databases-With-TypeORM.md)

*Data And Configuration*

# Lesson 11 - Configuration And Environments

## 11.1 The config module

Apps need settings that change between machines: ports, database URLs, API keys. These belong in **environment variables**, not in code. Nest provides `@nestjs/config` to load and read them.

```bash
npm install @nestjs/config
```

Create a `.env` file in the project root:

```
PORT=3000
DATABASE_URL=postgres://user:pass@localhost:5432/mydb
JWT_SECRET=change-me
```

Register the module:

```ts
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true, // no need to import ConfigModule in every module
    }),
  ],
})
export class AppModule {}
```

Read values with `ConfigService`:

```ts
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AppService {
  constructor(private readonly config: ConfigService) {}

  getPort(): number {
    return this.config.get<number>('PORT', 3000); // second arg is the default
  }

  getJwtSecret(): string {
    return this.config.getOrThrow<string>('JWT_SECRET'); // throws if missing
  }
}
```

Use it in `main.ts` too:

```ts
const app = await NestFactory.create(AppModule);
const config = app.get(ConfigService);
await app.listen(config.get<number>('PORT', 3000));
```

Note that environment variable values are always **strings** unless you transform them (see 11.2).

---

## 11.2 Validating environment variables

A missing or wrong variable should crash the app **at startup**, not hours later. Provide a `validate` function using `class-validator` and `class-transformer`:

```ts
// src/config/env.validation.ts
import { plainToInstance } from 'class-transformer';
import { IsEnum, IsInt, IsString, Max, Min, validateSync } from 'class-validator';

enum Environment {
  Development = 'development',
  Production = 'production',
  Test = 'test',
}

class EnvironmentVariables {
  @IsEnum(Environment)
  NODE_ENV: Environment = Environment.Development;

  @IsInt()
  @Min(0)
  @Max(65535)
  PORT: number = 3000;

  @IsString()
  DATABASE_URL: string;

  @IsString()
  JWT_SECRET: string;
}

export function validate(config: Record<string, unknown>) {
  const validated = plainToInstance(EnvironmentVariables, config, {
    enableImplicitConversion: true,
  });
  const errors = validateSync(validated, { skipMissingProperties: false });

  if (errors.length > 0) {
    throw new Error(errors.toString());
  }
  return validated;
}
```

```ts
ConfigModule.forRoot({ isGlobal: true, validate })
```

`enableImplicitConversion` turns `"3000"` into the number `3000` using the declared type.

---

## 11.3 Per-environment settings

Load different files per environment with `envFilePath`. Files earlier in the list take priority.

```ts
ConfigModule.forRoot({
  isGlobal: true,
  envFilePath: [`.env.${process.env.NODE_ENV}.local`, `.env.${process.env.NODE_ENV}`, '.env'],
})
```

Run with: `NODE_ENV=production node dist/main`. (On Windows PowerShell: `$env:NODE_ENV="production"; node dist/main`.)

Organize related settings into **namespaced configuration** with `registerAs`:

```ts
// src/config/database.config.ts
import { registerAs } from '@nestjs/config';

export default registerAs('database', () => ({
  url: process.env.DATABASE_URL,
  poolSize: parseInt(process.env.DB_POOL_SIZE ?? '10', 10),
}));
```

```ts
ConfigModule.forRoot({ isGlobal: true, load: [databaseConfig] })
```

```ts
const url = this.config.get<string>('database.url');
```

In deployed environments (Docker, cloud platforms), you usually set real environment variables on the platform and do not ship a `.env` file at all. Real environment variables take priority over `.env` file values.

---

## 11.4 Secrets handling

Secrets are passwords, API keys, tokens, and private keys. Rules to follow:

- **Never commit `.env` files.** Add them to `.gitignore`:

```
.env
.env.*
!.env.example
```

- **Commit a `.env.example`** listing the variable names with fake values so teammates know what to set:

```
PORT=3000
DATABASE_URL=postgres://user:password@localhost:5432/dbname
JWT_SECRET=replace-me
```

- **Use a secrets manager in production** (for example AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager, or your platform's secret store) and inject values as environment variables.
- **Never log secrets** or return them from endpoints.
- **Use different secrets per environment** so a leaked development key cannot harm production.
- **Rotate secrets** if they are ever exposed, including any that appeared in git history.
- **Use `getOrThrow`** for required secrets so the app fails fast when one is missing.

---

[Previous](./[10]-Request-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[12]-Databases-With-TypeORM.md)