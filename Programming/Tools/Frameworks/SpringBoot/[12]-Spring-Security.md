[Previous](./[11]-Other-Data-Sources.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[13]-JWT-And-OAuth2.md)

*Security*

# Lesson 12 - Spring Security

Spring Security is the standard framework for protecting Spring applications. Adding the starter immediately secures every endpoint, and you then customize the rules.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Right after adding it, all endpoints require login. Boot generates a user named `user` with a random password printed in the startup log.

---

## 12.1 Authentication and authorization

| Term | Question it answers | Example |
|---|---|---|
| **Authentication** | *Who are you?* | Verifying a username and password |
| **Authorization** | *What are you allowed to do?* | Only admins may delete users |

Failure responses:
- Not authenticated → **401 Unauthorized** (or a redirect to the login page).
- Authenticated but not allowed → **403 Forbidden**.

### Key concepts

| Concept | Meaning |
|---|---|
| **Principal** | The currently authenticated user |
| **Authentication** | An object holding the principal, credentials, and authorities |
| **GrantedAuthority** | A permission or role (`ROLE_ADMIN`, `read:orders`) |
| **SecurityContext** | Holds the current `Authentication` for the running request |
| **UserDetails** | Spring's representation of a user |
| **UserDetailsService** | Loads a user by username |

By convention, roles are authorities prefixed with `ROLE_`. `hasRole("ADMIN")` checks the authority `ROLE_ADMIN`.

### Accessing the current user

```java
@GetMapping("/me")
public String me(Authentication authentication) {
    return authentication.getName();
}

@GetMapping("/me2")
public String me2(@AuthenticationPrincipal UserDetails user) {
    return user.getUsername();
}
```

### Sessions vs stateless

- **Session-based** (classic web apps): after login the server stores the session and the browser sends a session cookie.
- **Stateless** (REST APIs): every request carries credentials or a token (Lesson 13).

---

## 12.2 The security filter chain

Security is implemented as a **chain of servlet filters** that every request passes through *before* reaching your controllers.

```
Request -> [CORS] -> [CSRF] -> [Authentication] -> [Authorization] -> Controller
```

Each filter has one job, such as reading credentials, checking CSRF tokens, or enforcing access rules. You configure the chain by defining a `SecurityFilterChain` bean.

### A typical configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/", "/public/**", "/css/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers(HttpMethod.GET, "/api/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated())
            .formLogin(Customizer.withDefaults())
            .logout(logout -> logout.logoutSuccessUrl("/"));

        return http.build();
    }
}
```

### Rule ordering

Rules are evaluated **top to bottom, first match wins**. Put specific rules first and `anyRequest()` last.

### Matchers

| Method | Meaning |
|---|---|
| `permitAll()` | Anyone, including anonymous |
| `authenticated()` | Any logged-in user |
| `hasRole("X")` / `hasAnyRole(...)` | Role check |
| `hasAuthority("x")` | Exact authority check |
| `denyAll()` | Nobody |

### A user store for development

```java
@Bean
public UserDetailsService users(PasswordEncoder encoder) {
    UserDetails user = User.withUsername("ana")
            .password(encoder.encode("password"))
            .roles("USER")
            .build();
    UserDetails admin = User.withUsername("boss")
            .password(encoder.encode("admin123"))
            .roles("ADMIN")
            .build();
    return new InMemoryUserDetailsManager(user, admin);
}
```

### A database-backed user store

```java
@Service
public class DbUserDetailsService implements UserDetailsService {

    private final UserRepository users;

    public DbUserDetailsService(UserRepository users) { this.users = users; }

    @Override
    public UserDetails loadUserByUsername(String username) {
        AppUser u = users.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found"));
        return User.withUsername(u.getUsername())
                .password(u.getPasswordHash())          // already hashed
                .roles(u.getRoles().toArray(String[]::new))
                .build();
    }
}
```

Spring Security uses this service automatically when it is the only `UserDetailsService` bean.

### Stateless REST API configuration

```java
http
    .csrf(csrf -> csrf.disable())                          // only for non-browser, token-based APIs
    .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
    .httpBasic(Customizer.withDefaults())
    .authorizeHttpRequests(a -> a.anyRequest().authenticated());
```

Only disable CSRF when the API is not used from browsers with cookie-based sessions.

---

## 12.3 Password encoding

**Never store plain-text passwords.** Store a **hash** that is slow to compute and uses a unique random salt per password.

### Defining the encoder

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();                    // strength 10 by default
}
```

Or use the flexible default:

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return PasswordEncoderFactories.createDelegatingPasswordEncoder();
}
```

### Using it

```java
@Service
public class RegistrationService {

    private final UserRepository users;
    private final PasswordEncoder encoder;

    public RegistrationService(UserRepository users, PasswordEncoder encoder) {
        this.users = users;
        this.encoder = encoder;
    }

    public void register(String username, String rawPassword) {
        String hash = encoder.encode(rawPassword);          // e.g. $2a$10$N9qo8uLOickgx2ZMRZoMye...
        users.save(new AppUser(username, hash));
    }

    public boolean matches(String raw, String stored) {
        return encoder.matches(raw, stored);                // never compare strings yourself
    }
}
```

### Facts to remember

- The same password encodes to a **different hash each time** (random salt), so use `matches()`, never `equals()`.
- Hashing is **one-way**. You cannot recover the original password.
- **BCrypt** is a solid default. **Argon2** (`Argon2PasswordEncoder`) is a modern alternative that is also recommended.
- The `DelegatingPasswordEncoder` stores a prefix such as `{bcrypt}` so you can upgrade algorithms later while old hashes still verify.
- Never use MD5 or plain SHA-1/SHA-256 for passwords. They are far too fast to compute.
- Never log passwords or tokens.

### Related practices

- Enforce a minimum length; length matters more than complexity rules.
- Limit login attempts (rate limit or temporary lockout).
- Serve everything over HTTPS.

---

## 12.4 Method-level security

URL rules are coarse. **Method security** protects individual service methods, including row-level rules like "users may edit only their own data".

### Enabling it

```java
@Configuration
@EnableMethodSecurity
public class MethodSecurityConfig { }
```

### Annotations

```java
@Service
public class DocumentService {

    @PreAuthorize("hasRole('ADMIN')")
    public void deleteAll() { ... }

    @PreAuthorize("hasAnyRole('USER','ADMIN')")
    public List<Document> list() { ... }

    @PreAuthorize("#username == authentication.name")
    public Profile getProfile(String username) { ... }          // own profile only

    @PostAuthorize("returnObject.owner == authentication.name")
    public Document load(Long id) { ... }                       // checks AFTER the method runs

    @PreAuthorize("hasAuthority('SCOPE_reports.read')")
    public Report report() { ... }
}
```

| Annotation | When it runs |
|---|---|
| `@PreAuthorize` | Before the method; the most common choice |
| `@PostAuthorize` | After the method, with access to `returnObject` |
| `@PreFilter` / `@PostFilter` | Filters collection arguments or results |
| `@Secured` | Simple role check without expressions |

### A custom bean in expressions

```java
@Component("perm")
public class DocumentPermissions {
    public boolean canEdit(Long docId, Authentication auth) { ... }
}

@PreAuthorize("@perm.canEdit(#id, authentication)")
public void edit(Long id) { ... }
```

### Failures

A denied call throws `AccessDeniedException`, which the framework turns into a 403. Handle it in a `@RestControllerAdvice` if you want a custom body.

### Testing secured code

```java
@Test
@WithMockUser(roles = "ADMIN")
void adminCanDelete() { ... }

@Test
@WithMockUser(roles = "USER")
void userCannotDelete() {
    assertThrows(AccessDeniedException.class, () -> service.deleteAll());
}
```

(`@WithMockUser` comes from `spring-security-test`.)

### Defense in depth

Combine both layers: URL rules to block whole areas, and method security to guard the business logic. Method security still protects you if the logic is later called from a new controller, a scheduler, or a message listener.

[Previous](./[11]-Other-Data-Sources.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[13]-JWT-And-OAuth2.md)