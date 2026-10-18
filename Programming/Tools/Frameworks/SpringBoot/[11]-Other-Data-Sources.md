[Previous](./[10]-Transactions-And-Migrations.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[12]-Spring-Security.md)

*Data*

# Lesson 11 - Other Data Sources

Relational databases are not the only option. Spring Data has a consistent programming model for many stores, and Spring has built-in support for caching and connection pooling.

---

## 11.1 MongoDB and Redis

### MongoDB (document database)

MongoDB stores JSON-like **documents** in **collections**. It suits data with a flexible or nested shape.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

```properties
spring.data.mongodb.uri=mongodb://localhost:27017/shop
```

```java
@Document(collection = "products")
public class Product {

    @Id
    private String id;                    // ObjectId stored as a String

    private String name;
    private double price;
    private List<String> tags = new ArrayList<>();
    private Map<String, String> attributes = new HashMap<>();   // flexible fields

    // constructors, getters, setters
}

public interface ProductRepository extends MongoRepository<Product, String> {
    List<Product> findByTagsContaining(String tag);
    List<Product> findByPriceLessThan(double max);
}
```

The repository style matches JPA: derived query methods, paging, and `save`/`findById`.

### Redis (in-memory key-value store)

Redis keeps data in memory and is extremely fast. Typical uses: caching, sessions, rate limiting, leaderboards, and pub/sub.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```properties
spring.data.redis.host=localhost
spring.data.redis.port=6379
```

```java
@Service
public class LoginAttemptService {

    private final StringRedisTemplate redis;

    public LoginAttemptService(StringRedisTemplate redis) { this.redis = redis; }

    public long recordFailure(String username) {
        String key = "login:fail:" + username;
        Long count = redis.opsForValue().increment(key);
        redis.expire(key, Duration.ofMinutes(15));        // auto-delete the key
        return count == null ? 0 : count;
    }

    public boolean isLocked(String username) {
        String value = redis.opsForValue().get("login:fail:" + username);
        return value != null && Long.parseLong(value) >= 5;
    }
}
```

`StringRedisTemplate` is for string keys and values. `RedisTemplate<K,V>` handles other types with a serializer you choose.

### Redis data structures

| Structure | Accessor | Example use |
|---|---|---|
| String | `opsForValue()` | Counters, cached values |
| Hash | `opsForHash()` | Object fields |
| List | `opsForList()` | Queues |
| Set | `opsForSet()` | Unique tags |
| Sorted set | `opsForZSet()` | Leaderboards |

---

## 11.2 Caching abstraction

**Caching** stores the result of expensive work so repeated requests are fast. Spring's abstraction lets you add caching with annotations and swap the storage behind it.

### Enable it

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

```java
@SpringBootApplication
@EnableCaching
public class DemoApplication { ... }
```

### The main annotations

```java
@Service
public class ProductService {

    @Cacheable(cacheNames = "products", key = "#id")
    public Product find(Long id) {
        // runs only on a cache miss
        return repo.findById(id).orElseThrow();
    }

    @CachePut(cacheNames = "products", key = "#product.id")
    public Product update(Product product) {
        return repo.save(product);                // always runs, then refreshes the cache entry
    }

    @CacheEvict(cacheNames = "products", key = "#id")
    public void delete(Long id) {
        repo.deleteById(id);                      // removes the cached entry
    }

    @CacheEvict(cacheNames = "products", allEntries = true)
    public void clearAll() { }
}
```

| Annotation | Behavior |
|---|---|
| `@Cacheable` | Return the cached value if present; otherwise run and store the result |
| `@CachePut` | Always run the method and update the cache |
| `@CacheEvict` | Remove one or all entries |
| `@Caching` | Combine several cache operations |

### Choosing a cache provider

By default, Boot uses a simple in-memory `ConcurrentHashMap`, which is fine for demos but has no expiry or size limit. For real use:

**Caffeine** (local, in-process, with expiry and size limits):

```properties
spring.cache.type=caffeine
spring.cache.caffeine.spec=maximumSize=500,expireAfterWrite=10m
```

**Redis** (shared across multiple app instances):

```properties
spring.cache.type=redis
spring.cache.redis.time-to-live=10m
```

### Rules of thumb

- Cache data that is **read often and changes rarely**.
- Always define an expiry, or stale data lives forever.
- Cache keys must be unique per input. Use `key` expressions to build them.
- Cached objects stored in Redis must be serializable.
- The same self-invocation limit as `@Transactional` applies: annotations only work on calls coming through the Spring proxy.
- Cache invalidation is hard. Prefer short expiry times when exact freshness is not critical.

---

## 11.3 Connection pools

Opening a database connection is slow (network handshake, authentication). A **connection pool** keeps a set of open connections and lends them out, which makes requests much faster.

### HikariCP

Boot uses **HikariCP** by default when you use JDBC or JPA, with no extra setup.

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/app
spring.datasource.username=app
spring.datasource.password=${DB_PASSWORD}

spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=2
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

| Property | Meaning |
|---|---|
| `maximum-pool-size` | Most connections open at once |
| `minimum-idle` | Idle connections kept ready |
| `connection-timeout` | How long a caller waits for a free connection (ms) |
| `idle-timeout` | When to close unused connections (ms) |
| `max-lifetime` | Recycle a connection after this long (ms); keep it below the database's own limit |

### Sizing the pool

More is not better. The database has limited CPU and each connection costs memory. A pool of around 10 is often enough for a single app instance. Remember that **instances × pool size** must stay below the database's `max_connections`.

### Typical symptoms and fixes

| Symptom | Likely cause |
|---|---|
| `Connection is not available, request timed out` | Pool exhausted: long transactions or connection leaks |
| Slow requests under load | Pool too small, or slow queries holding connections |
| `Too many connections` from the DB | Total pool size across instances is too large |

Avoid long transactions and never call slow remote services while holding a connection.

### Redis and HTTP pools

Redis clients (Lettuce, Jedis) and HTTP clients also have pool-style settings. The same principle applies: reuse expensive connections and limit their number.

---

## 11.4 Choosing a data store

### Quick comparison

| Store | Type | Strengths | Weaknesses |
|---|---|---|---|
| PostgreSQL / MySQL | Relational | Transactions, joins, strong consistency, mature tooling | Harder to scale writes horizontally |
| MongoDB | Document | Flexible schema, nested data, horizontal scaling | Weaker joins; transactions are more limited |
| Redis | Key-value (in-memory) | Very fast, rich data structures | Memory-bound; persistence is secondary |
| Elasticsearch / OpenSearch | Search engine | Full-text search and analytics | Not a primary source of truth |
| Cassandra | Wide-column | Massive write scale | Query patterns must be designed up front |

### Decision guide

- **Default choice: a relational database.** Most business applications have structured, related data and need transactions.
- Choose **MongoDB** when records are naturally self-contained documents with varying fields, and you rarely join across them.
- Add **Redis** as a **complement**: caching, sessions, counters, rate limiting. It usually sits next to a main database.
- Add a **search engine** when users need relevance-ranked text search that SQL `LIKE` handles poorly.

### Polyglot persistence

One application can use several stores, each for what it does best, for example:

```
PostgreSQL     -> orders, customers, payments
Redis          -> sessions, cache, rate limits
Elasticsearch  -> product search
```

The cost is operational complexity: more systems to run, back up, secure, and keep in sync. Add a store only when a concrete need justifies it.

### Questions to ask

1. Is the data highly relational? → relational DB.
2. Do I need multi-row ACID transactions? → relational DB.
3. Does the shape change often and live in a single document? → document DB.
4. Is it temporary or recomputable and speed-critical? → Redis.
5. Is the main need searching text? → search engine.

[Previous](./[10]-Transactions-And-Migrations.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[12]-Spring-Security.md)