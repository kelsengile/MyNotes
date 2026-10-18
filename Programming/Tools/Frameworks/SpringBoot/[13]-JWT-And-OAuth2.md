[Previous](./[12]-Spring-Security.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[14]-Actuator-And-Monitoring.md)

*Security*

# Lesson 13 - JWT And OAuth2

Modern APIs often avoid server-side sessions and instead use **tokens**. This lesson covers JSON Web Tokens, protecting an API as a resource server, social login with OAuth2, and the browser security features CORS and CSRF.

---

## 13.1 Token-based authentication

### The flow

```
1. Client sends credentials to a login endpoint (or an identity provider).
2. Server verifies them and returns a signed token.
3. Client sends the token on every request: Authorization: Bearer <token>
4. Server validates the token's signature and expiry. No session lookup is needed.
```

### Sessions vs tokens

| | Server session | Token (JWT) |
|---|---|---|
| State stored on the server | Yes | No (stateless) |
| Scaling across instances | Needs a shared session store | Easy |
| Revocation | Easy (delete the session) | Harder (wait for expiry or keep a denylist) |
| Best for | Server-rendered web apps | APIs, mobile apps, microservices |

### Anatomy of a JWT

A JWT has three Base64URL parts separated by dots: `header.payload.signature`.

```json
// Header
{ "alg": "RS256", "typ": "JWT" }

// Payload (claims)
{
  "sub": "ana",
  "iss": "https://auth.example.com",
  "iat": 1760000000,
  "exp": 1760003600,
  "scope": "orders.read orders.write",
  "roles": ["USER"]
}
```

| Claim | Meaning |
|---|---|
| `sub` | Subject (who the token is about) |
| `iss` | Issuer |
| `aud` | Intended audience |
| `iat` / `exp` | Issued at / expires at (Unix time) |

### Critical facts

- The payload is **encoded, not encrypted**. Anyone can read it, so **never put secrets in it**.
- The **signature** proves the token was not altered and came from the trusted issuer.
- Keep **access tokens short-lived** (minutes), and use **refresh tokens** to obtain new ones.
- Always send tokens over HTTPS.
- Always verify the signature, expiry, issuer, and audience. Never accept `alg: none`.

### Algorithms

- **HS256** — symmetric (one shared secret). Simple, but every verifier knows the signing secret.
- **RS256 / ES256** — asymmetric. The issuer signs with a private key, and verifiers need only the public key. Preferred when several services validate tokens.

---

## 13.2 JWT resource server

A **resource server** is your API. It does not issue tokens. It only accepts and validates them. Tokens come from an **authorization server** (Keycloak, Auth0, Okta, Microsoft Entra ID, Spring Authorization Server, and so on).

### Dependency

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
```

### Configuration

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth.example.com/realms/demo
          # or, without discovery:
          # jwk-set-uri: https://auth.example.com/.well-known/jwks.json
```

Spring downloads the issuer's public keys from the JWK set and uses them to verify signatures.

### Security configuration

```java
@Configuration
@EnableWebSecurity
public class ApiSecurityConfig {

    @Bean
    public SecurityFilterChain api(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())                        // token API, no cookies
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasAuthority("SCOPE_admin")
                .anyRequest().authenticated())
            .oauth2ResourceServer(oauth -> oauth
                .jwt(jwt -> jwt.jwtAuthenticationConverter(jwtConverter())));
        return http.build();
    }
```

By default, the `scope` claim becomes authorities prefixed with `SCOPE_` (so `orders.read` becomes `SCOPE_orders.read`).

### Mapping custom role claims

```java
    @Bean
    JwtAuthenticationConverter jwtConverter() {
        JwtGrantedAuthoritiesConverter authorities = new JwtGrantedAuthoritiesConverter();
        authorities.setAuthoritiesClaimName("roles");     // read from the "roles" claim
        authorities.setAuthorityPrefix("ROLE_");

        JwtAuthenticationConverter converter = new JwtAuthenticationConverter();
        converter.setJwtGrantedAuthoritiesConverter(authorities);
        return converter;
    }
}
```

Providers place roles in different claims (Keycloak, for example, nests them under `realm_access.roles`), so check your token and adapt the converter.

### Reading token data in a controller

```java
@GetMapping("/api/me")
public Map<String, Object> me(@AuthenticationPrincipal Jwt jwt) {
    return Map.of(
        "subject", jwt.getSubject(),
        "scopes", jwt.getClaimAsString("scope")
    );
}
```

### Calling the API

```bash
curl -H "Authorization: Bearer eyJhbGciOi..." http://localhost:8080/api/me
```

| Response | Meaning |
|---|---|
| 401 | Missing, invalid, or expired token |
| 403 | Valid token but insufficient authority |

### Issuing your own tokens

For small projects you can sign tokens yourself with Spring's `JwtEncoder` (backed by Nimbus). For anything beyond a simple project, use a dedicated authorization server rather than building token issuance, refresh, and revocation yourself.

---

## 13.3 OAuth2 login

**OAuth 2.0** lets a user grant your app limited access without sharing their password. **OpenID Connect (OIDC)** is a layer on top of it that adds identity ("log in with Google").

### Roles in the flow

| Role | Example |
|---|---|
| Resource owner | The user |
| Client | Your Spring Boot app |
| Authorization server | Google, GitHub, Keycloak |
| Resource server | The API holding the user's data |

### Authorization Code flow (simplified)

```
1. User clicks "Log in with Google".
2. App redirects the browser to Google with client_id, scope, redirect_uri.
3. User signs in and consents at Google.
4. Google redirects back to your app with a one-time code.
5. App exchanges the code (plus its client secret) for tokens, server to server.
6. App creates a logged-in session for the user.
```

### Dependency

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
```

### Configuration

Register your app in the provider's console (Google Cloud, GitHub Developer Settings) to get a client ID and secret, with the redirect URI set to `http://localhost:8080/login/oauth2/code/google`.

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: ${GOOGLE_CLIENT_ID}
            client-secret: ${GOOGLE_CLIENT_SECRET}
            scope: openid, profile, email
          github:
            client-id: ${GITHUB_CLIENT_ID}
            client-secret: ${GITHUB_CLIENT_SECRET}
            scope: read:user
```

Google and GitHub are **known providers**, so Boot already knows their endpoints.

### Enabling login

```java
@Bean
public SecurityFilterChain web(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(a -> a
            .requestMatchers("/", "/error").permitAll()
            .anyRequest().authenticated())
        .oauth2Login(Customizer.withDefaults());
    return http.build();
}
```

### Using the logged-in user

```java
@GetMapping("/profile")
public Map<String, Object> profile(@AuthenticationPrincipal OidcUser user) {
    return Map.of("name", user.getFullName(), "email", user.getEmail());
}
// For non-OIDC providers like GitHub, use OAuth2User and user.getAttribute("login").
```

### Security notes

- Keep the **client secret** out of source control; use environment variables.
- Register only the exact redirect URIs you need.
- Request the minimum scopes.
- For single-page and mobile apps, use Authorization Code with **PKCE**, with no client secret in the app.

---

## 13.4 CORS and CSRF

### CORS (Cross-Origin Resource Sharing)

Browsers block JavaScript on `https://app.example.com` from reading responses from `https://api.example.com` unless the API explicitly allows it. This is the **same-origin policy**. CORS is how the API opts in.

An **origin** is scheme + host + port, so `http://localhost:3000` and `http://localhost:8080` are different origins.

Configure it in the security chain:

```java
@Bean
public SecurityFilterChain chain(HttpSecurity http) throws Exception {
    http.cors(Customizer.withDefaults());       // uses the CorsConfigurationSource bean below
    // ... other configuration
    return http.build();
}

@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("https://app.example.com", "http://localhost:3000"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    config.setAllowCredentials(true);
    config.setMaxAge(3600L);

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/api/**", config);
    return source;
}
```

Notes:

- Browsers first send a **preflight** `OPTIONS` request for non-simple requests. The CORS filter must answer it before authentication.
- Do not use `"*"` as an allowed origin together with `allowCredentials(true)`.
- CORS is a **browser** rule. Tools like curl and other servers ignore it, so it is not a replacement for authentication.

### CSRF (Cross-Site Request Forgery)

An attacker's page makes the victim's browser send a request (such as a transfer) to your site, and the browser automatically attaches the victim's **session cookie**. CSRF protection requires a secret token that the attacker's page cannot read.

| Situation | CSRF protection |
|---|---|
| Browser app using **session cookies** (forms, Thymeleaf) | **Keep it enabled** (the default) |
| Stateless API using an `Authorization: Bearer` header | Safe to disable, because browsers do not attach that header automatically |
| SPA using cookie-based auth | Keep it enabled; use `CookieCsrfTokenRepository` |

```java
// SPA reading the token from a cookie
http.csrf(csrf -> csrf.csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse()));

// Stateless token API
http.csrf(csrf -> csrf.disable());
```

Disabling CSRF is only acceptable when authentication does not rely on cookies the browser sends automatically.

### Other defenses worth enabling

- Cookie flags: `HttpOnly`, `Secure`, and `SameSite=Lax` or `Strict`.
- Security headers (Spring Security sets sensible defaults such as `X-Content-Type-Options`).
- HTTPS everywhere (`server.ssl.*` or terminate TLS at a proxy and enable forwarded header handling).

[Previous](./[12]-Spring-Security.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[14]-Actuator-And-Monitoring.md)