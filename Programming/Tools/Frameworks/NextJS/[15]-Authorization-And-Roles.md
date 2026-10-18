[Previous](./[14]-Authentication-With-Passport-And-JWT.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[16]-API-Security.md)

*Security*

# Lesson 15 - Authorization And Roles

## 15.1 Role-based access control

**Authentication** proves who you are. **Authorization** decides what you may do. **Role-based access control (RBAC)** gives each user one or more roles (such as `user` or `admin`), and rules are written in terms of roles.

Define roles in one place:

```ts
// src/auth/role.enum.ts
export enum Role {
  User = 'user',
  Admin = 'admin',
}
```

Make sure the role is available on `request.user`. If it is stored in the database, include it in the JWT payload (or load the user in `JwtStrategy.validate`):

```ts
// in JwtStrategy
async validate(payload: { sub: number; email: string; roles: Role[] }) {
  return { userId: payload.sub, email: payload.email, roles: payload.roles };
}
```

---

## 15.2 Custom decorators

Create a `@Roles()` decorator that stores the required roles as metadata:

```ts
// src/auth/roles.decorator.ts
import { SetMetadata } from '@nestjs/common';
import { Role } from './role.enum';

export const ROLES_KEY = 'roles';
export const Roles = (...roles: Role[]) => SetMetadata(ROLES_KEY, roles);
```

A guard reads that metadata and compares it with the user's roles:

```ts
// src/auth/roles.guard.ts
import { CanActivate, ExecutionContext, Injectable } from '@nestjs/common';
import { Reflector } from '@nestjs/core';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private readonly reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const required = this.reflector.getAllAndOverride<Role[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);

    if (!required || required.length === 0) {
      return true; // no roles required
    }

    const { user } = context.switchToHttp().getRequest();
    return required.some((role) => user?.roles?.includes(role));
  }
}
```

Use it:

```ts
@Controller('admin')
@UseGuards(JwtAuthGuard, RolesGuard) // order matters: authenticate first
export class AdminController {
  @Get('stats')
  @Roles(Role.Admin)
  stats() {
    return { users: 100 };
  }
}
```

A **parameter decorator** makes controllers cleaner by extracting the current user:

```ts
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: string | undefined, ctx: ExecutionContext) => {
    const user = ctx.switchToHttp().getRequest().user;
    return data ? user?.[data] : user;
  },
);

// Usage
@Get('me')
me(@CurrentUser() user, @CurrentUser('email') email: string) {}
```

---

## 15.3 Policies and permissions

Roles are simple but coarse. Real apps often need **fine-grained** rules, such as "a user can edit only their **own** posts" or "editors can publish but not delete".

Two common approaches:

**Permissions instead of roles.** Check specific actions, and map roles to permissions:

```ts
export enum Permission {
  PostCreate = 'post:create',
  PostDelete = 'post:delete',
}

const rolePermissions: Record<Role, Permission[]> = {
  [Role.User]: [Permission.PostCreate],
  [Role.Admin]: [Permission.PostCreate, Permission.PostDelete],
};
```

**Policy checks for ownership rules.** Put the logic in the service where the data is available:

```ts
async update(postId: number, user: { userId: number; roles: Role[] }, dto: UpdatePostDto) {
  const post = await this.posts.findOneByOrFail({ id: postId });

  const isOwner = post.authorId === user.userId;
  const isAdmin = user.roles.includes(Role.Admin);

  if (!isOwner && !isAdmin) {
    throw new ForbiddenException('You can only edit your own posts');
  }
  return this.posts.save({ ...post, ...dto });
}
```

For large rule sets, the **CASL** library (`@casl/ability`) is the approach recommended in the Nest documentation. It lets you define abilities such as `can('update', 'Post', { authorId: user.id })` and check them in a guard.

---

## 15.4 Protecting routes

Choose a default and be explicit about exceptions. The safest default is **everything is protected**, and you mark public routes.

```ts
// public.decorator.ts
export const IS_PUBLIC_KEY = 'isPublic';
export const Public = () => SetMetadata(IS_PUBLIC_KEY, true);
```

```ts
// jwt-auth.guard.ts
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  constructor(private reflector: Reflector) {
    super();
  }

  canActivate(context: ExecutionContext) {
    const isPublic = this.reflector.getAllAndOverride<boolean>(IS_PUBLIC_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);
    if (isPublic) return true;
    return super.canActivate(context);
  }
}
```

Register both guards globally; order in the providers array is the order they run:

```ts
@Module({
  providers: [
    { provide: APP_GUARD, useClass: JwtAuthGuard },
    { provide: APP_GUARD, useClass: RolesGuard },
  ],
})
export class AuthModule {}
```

```ts
@Public()
@Post('login')
login() {}
```

Summary of status codes:

| Situation | Status |
|---|---|
| No token, bad token, expired token | `401 Unauthorized` |
| Valid user, but not allowed | `403 Forbidden` |

Also remember: hide data you should not return (never send password hashes), and authorize on the **server**. Hiding a button in the frontend is not security.

---

[Previous](./[14]-Authentication-With-Passport-And-JWT.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[16]-API-Security.md)