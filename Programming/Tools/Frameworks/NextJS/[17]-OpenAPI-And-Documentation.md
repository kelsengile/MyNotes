[Previous](./[16]-API-Security.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[18]-GraphQL-With-NestJS.md)

*Advanced Features*

# Lesson 17 - OpenAPI And Documentation

## 17.1 The Swagger module

**OpenAPI** is a standard format for describing REST APIs. **Swagger UI** turns that description into an interactive web page where you can read endpoints and try them out. Nest generates the OpenAPI document directly from your code.

```bash
npm install @nestjs/swagger
```

Set it up in `main.ts`:

```ts
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle('Cats API')
    .setDescription('The cats API description')
    .setVersion('1.0')
    .addBearerAuth() // enables the "Authorize" button for JWT
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('docs', app, document); // UI at /docs

  await app.listen(3000);
}
bootstrap();
```

Run the app and open `http://localhost:3000/docs`. The raw JSON is at `http://localhost:3000/docs-json`.

---

## 17.2 Decorating endpoints

Swagger discovers routes automatically, but decorators add meaning.

```ts
import {
  ApiBearerAuth, ApiOperation, ApiParam, ApiResponse, ApiTags,
} from '@nestjs/swagger';

@ApiTags('cats')           // groups endpoints in the UI
@ApiBearerAuth()           // marks these routes as needing a JWT
@Controller('cats')
export class CatsController {
  @Get(':id')
  @ApiOperation({ summary: 'Get one cat by id' })
  @ApiParam({ name: 'id', type: Number, description: 'The cat id' })
  @ApiResponse({ status: 200, description: 'The cat', type: CatDto })
  @ApiResponse({ status: 404, description: 'Cat not found' })
  findOne(@Param('id', ParseIntPipe) id: number) {
    return this.catsService.findOne(id);
  }
}
```

| Decorator | Purpose |
|---|---|
| `@ApiTags()` | Group endpoints |
| `@ApiOperation()` | Summary and description of an endpoint |
| `@ApiResponse()` | Document a response status and body type |
| `@ApiBearerAuth()` | Mark routes as requiring a bearer token |
| `@ApiQuery()` / `@ApiParam()` | Describe query and route parameters |
| `@ApiExcludeEndpoint()` | Hide an endpoint from the docs |

---

## 17.3 DTO documentation

Swagger can only describe request and response shapes if it can see their properties. Add `@ApiProperty()` to DTO fields:

```ts
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';
import { IsInt, IsOptional, IsString, Min } from 'class-validator';

export class CreateCatDto {
  @ApiProperty({ example: 'Tom', description: 'The name of the cat' })
  @IsString()
  name: string;

  @ApiProperty({ example: 3, minimum: 0 })
  @IsInt()
  @Min(0)
  age: number;

  @ApiPropertyOptional({ example: 'Siamese' })
  @IsOptional()
  @IsString()
  breed?: string;
}
```

To avoid writing `@ApiProperty()` on every field, enable the **Swagger CLI plugin** in `nest-cli.json`. It reads your TypeScript types and class-validator decorators automatically:

```json
{
  "collection": "@nestjs/schematics",
  "sourceRoot": "src",
  "compilerOptions": {
    "plugins": ["@nestjs/swagger"]
  }
}
```

For this plugin to detect DTOs, name files `*.dto.ts` or `*.entity.ts` (the default). When you use mapped types such as `PartialType`, import them from `@nestjs/swagger` instead of `@nestjs/mapped-types` so the documentation stays correct:

```ts
import { PartialType } from '@nestjs/swagger';

export class UpdateCatDto extends PartialType(CreateCatDto) {}
```

---

## 17.4 Generating clients

Because the OpenAPI document is machine-readable, tools can **generate client code** (typed API functions for a frontend or another service), keeping both sides in sync.

Common tools:

| Tool | Output |
|---|---|
| `openapi-typescript` | TypeScript types from the spec |
| `openapi-generator` | Clients in many languages |
| `orval` | TypeScript clients, optionally with React Query hooks |

Example workflow with `openapi-typescript`:

```bash
# 1. Start the API, then download the spec
curl http://localhost:3000/docs-json -o openapi.json

# 2. Generate types
npx openapi-typescript openapi.json -o src/api-types.ts
```

You can also write the spec to a file during build, without running the server, by calling `SwaggerModule.createDocument` in a script and saving it with `fs.writeFileSync('openapi.json', JSON.stringify(document, null, 2))`.

Tips:

- Version your API (`/v1/...`) and use `setVersion()`.
- Re-generate the client in CI so breaking changes are caught early.
- Keep response types documented with `type:` in `@ApiResponse()`; otherwise generated clients will return `unknown`.

---

[Previous](./[16]-API-Security.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[18]-GraphQL-With-NestJS.md)