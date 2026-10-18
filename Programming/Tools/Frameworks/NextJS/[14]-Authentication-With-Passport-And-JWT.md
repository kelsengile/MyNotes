[Previous](./[13]-Prisma-And-Mongoose.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[15]-Authorization-And-Roles.md)

*Security*

# Lesson 14 - Authentication With Passport And JWT

## 14.1 Local strategy

**Authentication** answers "who are you?". **Passport** is a library with many "strategies" (ways to authenticate). The **local** strategy checks a username and password.

```bash
npm install @nestjs/passport passport passport-local @nestjs/jwt passport-jwt bcrypt
npm install --save-dev @types/passport-local @types/passport-jwt @types/bcrypt
```

**Never store plain-text passwords.** Hash them with bcrypt when users register:

```ts
import * as bcrypt from 'bcrypt';

const passwordHash = await bcrypt.hash(dto.password, 10); // 10 = cost factor
await this.usersService.create({ ...dto, password: passwordHash });
```

The auth service verifies credentials:

```ts
@Injectable()
export class AuthService {
  constructor(
    private readonly usersService: UsersService,
    private readonly jwtService: JwtService,
  ) {}

  async validateUser(email: string, password: string) {
    const user = await this.usersService.findByEmail(email);
    if (user && (await bcrypt.compare(password, user.password))) {
      const { password: _omit, ...safeUser } = user;
      return safeUser;
    }
    return null;
  }
}
```

The local strategy calls `validateUser`:

```ts
import { Strategy } from 'passport-local';
import { PassportStrategy } from '@nestjs/passport';

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly authService: AuthService) {
    super({ usernameField: 'email' }); // default would be "username"
  }

  async validate(email: string, password: string) {
    const user = await this.authService.validateUser(email, password);
    if (!user) throw new UnauthorizedException('Invalid credentials');
    return user; // becomes request.user
  }
}
```

```ts
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class LocalAuthGuard extends AuthGuard('local') {}
```

---

## 14.2 JWT strategy

A **JSON Web Token (JWT)** is a signed string the server gives the client after login. The client sends it with every request, so the server does not need to store sessions. A JWT has three parts: header, payload (claims such as the user id), and signature. The payload is **encoded, not encrypted**, so never put secrets in it.

Configure the JWT module:

```ts
// auth.module.ts
@Module({
  imports: [
    UsersModule,
    PassportModule,
    JwtModule.registerAsync({
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        secret: config.getOrThrow<string>('JWT_SECRET'),
        signOptions: { expiresIn: '15m' },
      }),
    }),
  ],
  providers: [AuthService, LocalStrategy, JwtStrategy],
  controllers: [AuthController],
})
export class AuthModule {}
```

The JWT strategy verifies incoming tokens:

```ts
import { ExtractJwt, Strategy } from 'passport-jwt';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(config: ConfigService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: config.getOrThrow<string>('JWT_SECRET'),
    });
  }

  // Runs only if the signature and expiry are valid
  async validate(payload: { sub: number; email: string }) {
    return { userId: payload.sub, email: payload.email }; // becomes request.user
  }
}
```

```ts
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

---

## 14.3 Login and token flow

The flow:

1. Client sends email and password to `POST /auth/login`.
2. `LocalAuthGuard` verifies them and sets `request.user`.
3. The server signs and returns a JWT.
4. The client sends `Authorization: Bearer <token>` on later requests.
5. `JwtAuthGuard` verifies the token and sets `request.user`.

```ts
// auth.service.ts
async login(user: { id: number; email: string }) {
  const payload = { sub: user.id, email: user.email };
  return { access_token: await this.jwtService.signAsync(payload) };
}
```

```ts
// auth.controller.ts
@Controller('auth')
export class AuthController {
  constructor(private readonly authService: AuthService) {}

  @UseGuards(LocalAuthGuard)
  @Post('login')
  login(@Request() req) {
    return this.authService.login(req.user);
  }

  @UseGuards(JwtAuthGuard)
  @Get('profile')
  profile(@Request() req) {
    return req.user;
  }
}
```

Test with curl:

```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"a@b.com","password":"secret123"}'

curl http://localhost:3000/auth/profile -H "Authorization: Bearer <access_token>"
```

---

## 14.4 Refresh tokens

Short-lived access tokens limit damage if stolen, but forcing users to log in every 15 minutes is annoying. A **refresh token** is a longer-lived token used only to get new access tokens.

Design:

- **Access token:** lives about 15 minutes; sent with every API call.
- **Refresh token:** lives days or weeks; sent only to `POST /auth/refresh`; signed with a **different secret**.
- **Store a hash** of the refresh token on the server (like a password) so you can revoke it.
- **Rotate** on use: issue a new refresh token each time and invalidate the old one.

```ts
async issueTokens(user: { id: number; email: string }) {
  const payload = { sub: user.id, email: user.email };

  const [accessToken, refreshToken] = await Promise.all([
    this.jwtService.signAsync(payload, { expiresIn: '15m' }),
    this.jwtService.signAsync(payload, {
      secret: this.config.getOrThrow('JWT_REFRESH_SECRET'),
      expiresIn: '7d',
    }),
  ]);

  // Save a hash so the token can be revoked and compared later
  await this.usersService.setRefreshTokenHash(user.id, await bcrypt.hash(refreshToken, 10));

  return { accessToken, refreshToken };
}

async refresh(userId: number, presentedToken: string) {
  const user = await this.usersService.findById(userId);
  const valid = user?.refreshTokenHash && (await bcrypt.compare(presentedToken, user.refreshTokenHash));
  if (!valid) throw new ForbiddenException('Access denied');
  return this.issueTokens(user); // rotates the refresh token
}

async logout(userId: number) {
  await this.usersService.setRefreshTokenHash(userId, null); // revokes it
}
```

Security tips:

- Use HTTPS everywhere.
- In browsers, prefer storing refresh tokens in `httpOnly`, `secure` cookies rather than `localStorage`.
- Keep secrets in configuration (Lesson 11) and use long, random values.

---

[Previous](./[13]-Prisma-And-Mongoose.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[15]-Authorization-And-Roles.md)