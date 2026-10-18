[Previous](./[17]-OpenAPI-And-Documentation.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[19]-WebSockets-And-Real-Time.md)

*Advanced Features*

# Lesson 18 - GraphQL With NestJS

## 18.1 Code-first vs schema-first

**GraphQL** is an API style where the client asks for exactly the fields it wants through a single endpoint (`/graphql`). The server describes its capabilities in a **schema**.

Nest supports two ways to define that schema:

| Approach | You write | Nest generates | Good for |
|---|---|---|---|
| **Code-first** | TypeScript classes with decorators | The schema file (SDL) | TypeScript teams that want one source of truth |
| **Schema-first** | `.graphql` SDL files | TypeScript type definitions | Teams that design the schema before the code |

Install the packages (Apollo Server driver shown):

```bash
npm install @nestjs/graphql @nestjs/apollo @apollo/server graphql
```

Code-first setup:

```ts
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';
import { GraphQLModule } from '@nestjs/graphql';
import { join } from 'path';

@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'), // or `true` for in-memory
      sortSchema: true,
    }),
  ],
})
export class AppModule {}
```

Schema-first setup instead points to your SDL files:

```ts
GraphQLModule.forRoot<ApolloDriverConfig>({
  driver: ApolloDriver,
  typePaths: ['./**/*.graphql'],
})
```

The rest of this lesson uses code-first. Start the app and open `http://localhost:3000/graphql` to use the built-in playground/explorer.

---

## 18.2 Resolvers

In GraphQL, **resolvers** play the role controllers play in REST. First define the types:

```ts
import { Field, ID, Int, ObjectType } from '@nestjs/graphql';

@ObjectType()
export class Author {
  @Field(() => Int)
  id: number;

  @Field()
  name: string;

  @Field(() => [Post], { nullable: 'itemsAndList' })
  posts?: Post[];
}

@ObjectType()
export class Post {
  @Field(() => Int)
  id: number;

  @Field()
  title: string;

  @Field(() => Int)
  authorId: number;
}
```

Notice `@Field(() => Int)`: TypeScript's `number` is ambiguous (int or float), so GraphQL types are stated explicitly where needed. Strings and booleans are inferred.

Then write the resolver:

```ts
import { Args, Int, Query, Resolver, ResolveField, Parent } from '@nestjs/graphql';

@Resolver(() => Author)
export class AuthorsResolver {
  constructor(
    private readonly authorsService: AuthorsService,
    private readonly postsService: PostsService,
  ) {}

  @Query(() => Author, { name: 'author' })
  getAuthor(@Args('id', { type: () => Int }) id: number) {
    return this.authorsService.findOne(id);
  }

  @ResolveField('posts', () => [Post])
  posts(@Parent() author: Author) {
    return this.postsService.findByAuthor(author.id);
  }
}
```

Add the resolver to a module's `providers` (resolvers are providers, not controllers).

`@ResolveField()` computes a nested field only when the client actually asks for it.

---

## 18.3 Queries and mutations

A **query** reads data. A **mutation** changes data. Inputs use `@InputType()` classes, and validation works with the same class-validator decorators you already know.

```ts
import { Field, InputType } from '@nestjs/graphql';
import { IsString, MinLength } from 'class-validator';

@InputType()
export class CreatePostInput {
  @Field()
  @IsString()
  @MinLength(3)
  title: string;

  @Field(() => Int)
  authorId: number;
}
```

```ts
@Resolver(() => Post)
export class PostsResolver {
  constructor(private readonly postsService: PostsService) {}

  @Query(() => [Post], { name: 'posts' })
  findAll() {
    return this.postsService.findAll();
  }

  @Mutation(() => Post)
  createPost(@Args('input') input: CreatePostInput) {
    return this.postsService.create(input);
  }
}
```

Client requests:

```graphql
query {
  author(id: 1) {
    name
    posts {
      title
    }
  }
}

mutation {
  createPost(input: { title: "Hello GraphQL", authorId: 1 }) {
    id
    title
  }
}
```

Guards, pipes, interceptors, and filters work in GraphQL too, but the request lives in the GraphQL context. To read it in a guard:

```ts
import { GqlExecutionContext } from '@nestjs/graphql';

canActivate(context: ExecutionContext) {
  const ctx = GqlExecutionContext.create(context);
  const request = ctx.getContext().req;
  // ...use request.headers, request.user, etc.
  return true;
}
```

Note: set `context: ({ req }) => ({ req })` in `GraphQLModule.forRoot` so `req` is available in the context for your Apollo version.

---

## 18.4 Dataloaders

**The N+1 problem:** a query returns 100 posts, and a field resolver loads each post's author separately. That is 1 query for posts plus 100 queries for authors.

**DataLoader** batches and caches those lookups so the 100 author requests become a single query.

```bash
npm install dataloader
```

```ts
import DataLoader from 'dataloader';

@Injectable({ scope: Scope.REQUEST }) // a new loader per request so cache is not shared between users
export class AuthorsLoader {
  constructor(private readonly authorsService: AuthorsService) {}

  readonly byId = new DataLoader<number, Author>(async (ids) => {
    const authors = await this.authorsService.findByIds([...ids]);
    const map = new Map(authors.map((a) => [a.id, a]));
    // Results MUST be returned in the same order as the ids
    return ids.map((id) => map.get(id) ?? new Error(`Author ${id} not found`));
  });
}
```

```ts
@Resolver(() => Post)
export class PostsFieldResolver {
  constructor(private readonly loader: AuthorsLoader) {}

  @ResolveField(() => Author)
  author(@Parent() post: Post) {
    return this.loader.byId.load(post.authorId); // batched automatically
  }
}
```

Key rules:

- The batch function receives **all** keys requested in the same tick and must return results **in the same order**.
- Create loaders **per request** so cached data never leaks between users.
- Remember that request-scoped providers make dependents request-scoped (Lesson 5.3), which is an acceptable cost here.

---

[Previous](./[17]-OpenAPI-And-Documentation.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[19]-WebSockets-And-Real-Time.md)