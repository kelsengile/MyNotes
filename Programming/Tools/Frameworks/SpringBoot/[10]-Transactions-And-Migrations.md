[Previous](./[9]-Spring-Data-JPA.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[11]-Other-Data-Sources.md)

*Data*

# Lesson 10 - Transactions And Migrations

A **transaction** groups several database operations so they all succeed or all fail together. **Migrations** version your database schema the same way Git versions your code.

---

## 10.1 Transactional boundaries

### The classic example

```
Transfer 100 from account A to account B:
  1. subtract 100 from A
  2. add 100 to B
```

If the app crashes between the steps, money disappears. A transaction guarantees **all-or-nothing** behavior.

### ACID

| Letter | Property | Meaning |
|---|---|---|
| A | Atomicity | All steps happen, or none do |
| C | Consistency | Data rules always hold |
| I | Isolation | Concurrent transactions do not corrupt each other |
| D | Durability | Committed data survives crashes |

### `@Transactional`

```java
@Service
public class TransferService {

    private final AccountRepository accounts;

    public TransferService(AccountRepository accounts) { this.accounts = accounts; }

    @Transactional
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        Account from = accounts.findById(fromId).orElseThrow();
        Account to = accounts.findById(toId).orElseThrow();

        from.withdraw(amount);     // may throw if funds are insufficient
        to.deposit(amount);
        // commit happens automatically when the method returns normally
    }
}
```

Behavior:

- Spring opens a transaction before the method and **commits** on normal return.
- If a `RuntimeException` or `Error` escapes, it **rolls back**.
- Changes to managed entities are saved automatically at commit (**dirty checking**), so you do not even need `save()` inside the transaction.

### Rollback rules

```java
@Transactional(rollbackFor = Exception.class)             // also roll back on checked exceptions
@Transactional(noRollbackFor = NotificationException.class)
```

By default, **checked exceptions do not roll back**.

### Read-only transactions

```java
@Transactional(readOnly = true)
public List<Account> list() { return accounts.findAll(); }
```

This is a hint that allows optimizations. A common pattern is `@Transactional(readOnly = true)` on the class, with `@Transactional` on write methods.

### Where to put it

Put `@Transactional` on **service** methods, which represent a unit of business work, and not on controllers or repositories. Spring Data repository methods are already transactional individually.

### Pitfalls

- **Self-invocation**: calling a `@Transactional` method from another method of the *same class* bypasses the proxy, so no transaction starts. Move the method to another bean.
- **Private methods**: the annotation is ignored on private methods. Use public methods.
- **Swallowed exceptions**: catching an exception inside the method prevents rollback.
- Keep transactions **short**. Do not call slow external services inside one.

---

## 10.2 Isolation and propagation

### Isolation levels

Isolation controls what one transaction can see of another's uncommitted or concurrent changes.

| Level | Prevents |
|---|---|
| `READ_UNCOMMITTED` | Almost nothing (rarely used) |
| `READ_COMMITTED` | Dirty reads (the default in many databases, such as PostgreSQL) |
| `REPEATABLE_READ` | Dirty reads and non-repeatable reads (MySQL InnoDB default) |
| `SERIALIZABLE` | Everything, with the lowest concurrency |

Problems they address:

- **Dirty read** — reading another transaction's uncommitted data.
- **Non-repeatable read** — reading the same row twice and getting different values.
- **Phantom read** — repeating a query and getting different *rows*.

```java
@Transactional(isolation = Isolation.SERIALIZABLE)
public void criticalOperation() { ... }
```

The default (`DEFAULT`) uses the database's own setting, which is right for most cases.

### Optimistic locking

To prevent two users overwriting each other's edits, add a version field:

```java
@Entity
public class Product {
    @Id @GeneratedValue private Long id;
    @Version private Long version;      // incremented on each update
    private int stock;
}
```

If two transactions modify the same row, the second fails with `OptimisticLockingFailureException`. Catch it and retry or tell the user.

### Propagation

**Propagation** defines what happens when a transactional method calls another transactional method.

| Type | Behavior |
|---|---|
| `REQUIRED` (default) | Join the existing transaction, or start one if none |
| `REQUIRES_NEW` | Always start a new, independent transaction (suspending the current one) |
| `SUPPORTS` | Join if one exists, otherwise run without |
| `MANDATORY` | Must already be in a transaction, or throw |
| `NOT_SUPPORTED` | Run without a transaction |
| `NEVER` | Throw if a transaction exists |
| `NESTED` | Savepoint inside the current transaction (if supported) |

### Example: audit that survives rollback

```java
@Service
public class OrderService {

    private final AuditService audit;

    public OrderService(AuditService audit) { this.audit = audit; }

    @Transactional
    public void placeOrder(Order order) {
        audit.log("Order attempted");        // saved even if the order fails below
        validateAndSave(order);              // may throw, rolling back the order
    }
}

@Service
public class AuditService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void log(String message) { ... }  // commits on its own
}
```

`AuditService` is a separate bean, so the call goes through the proxy and `REQUIRES_NEW` works.

---

## 10.3 Flyway or Liquibase

### Why migrations

`ddl-auto=update` is convenient, but it cannot track history, cannot rename or move data safely, and gives no review trail. **Schema migration tools** apply versioned scripts in order and record what has run, so every environment ends up with an identical schema.

### Flyway

Add dependencies (newer Flyway versions need a database-specific module, such as `flyway-database-postgresql` for PostgreSQL):

```xml
<dependency>
  <groupId>org.flywaydb</groupId>
  <artifactId>flyway-core</artifactId>
</dependency>
<dependency>
  <groupId>org.flywaydb</groupId>
  <artifactId>flyway-database-postgresql</artifactId>
</dependency>
```

Create SQL files in `src/main/resources/db/migration/`:

```
db/migration/
├── V1__create_books_table.sql
├── V2__add_isbn_to_books.sql
└── V3__create_authors_table.sql
```

Naming: `V<version>__<description>.sql` (two underscores).

```sql
-- V1__create_books_table.sql
CREATE TABLE books (
    id      BIGSERIAL PRIMARY KEY,
    title   VARCHAR(200) NOT NULL,
    author  VARCHAR(100) NOT NULL,
    year    INT
);
```

```sql
-- V2__add_isbn_to_books.sql
ALTER TABLE books ADD COLUMN isbn VARCHAR(20);
```

Configure Hibernate to only **check** the schema:

```properties
spring.jpa.hibernate.ddl-auto=validate
```

On startup Flyway runs any pending scripts and records them in the `flyway_schema_history` table.

### Rules for migrations

- **Never edit** a migration that has already been applied. Add a new one instead (Flyway checks checksums and will fail).
- One logical change per file.
- Make destructive changes in steps (add the new column, copy data, then drop the old column in a later release).
- Use a repeatable `R__` script for views and stored procedures that should re-run when changed.

### Liquibase

Liquibase is the alternative. It describes changes in XML, YAML, JSON, or SQL "changelogs".

```yaml
# db/changelog/db.changelog-master.yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: ana
      changes:
        - createTable:
            tableName: books
            columns:
              - column: { name: id, type: BIGINT, autoIncrement: true, constraints: { primaryKey: true } }
              - column: { name: title, type: VARCHAR(200) }
```

| | Flyway | Liquibase |
|---|---|---|
| Format | Plain SQL (mostly) | XML/YAML/JSON/SQL |
| Learning curve | Lower | Slightly higher |
| Database-agnostic changes | Limited | Strong |
| Rollback support | Paid editions | Built in for many change types |

Pick one per project. Boot auto-configures either when it is on the classpath.

---

## 10.4 Testing with databases

### Slice test with `@DataJpaTest`

`@DataJpaTest` starts only the JPA layer (entities and repositories), uses an embedded database, and **rolls back each test**.

```java
@DataJpaTest
class BookRepositoryTest {

    @Autowired
    private BookRepository repo;

    @Test
    void findsBooksByAuthor() {
        repo.save(new Book("Dune", "Frank Herbert", 1965, Genre.FICTION));
        repo.save(new Book("Emma", "Jane Austen", 1815, Genre.FICTION));

        List<Book> result = repo.findByAuthor("Frank Herbert");

        assertThat(result).hasSize(1);
        assertThat(result.get(0).getTitle()).isEqualTo("Dune");
    }
}
```

### Test the real database engine

H2 behaves differently from PostgreSQL in places (SQL dialect, types, locking). To test against the real thing, use **Testcontainers** (Lesson 15), which runs a throwaway PostgreSQL in Docker:

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class PostgresRepositoryTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

    // tests as before; Flyway migrations run against the real engine
}
```

### Good habits

- Run Flyway migrations in tests too, to prove the scripts work from scratch.
- Do not share data between tests. Rely on rollback or clean up explicitly.
- Test custom `@Query` methods. Derived queries are rarely worth testing, but JPQL and native SQL are.
- If a test calls a transactional service, remember that `@DataJpaTest` wraps tests in a transaction. Use `@Transactional(propagation = NOT_SUPPORTED)` on the test to see real commit behavior.

[Previous](./[9]-Spring-Data-JPA.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[11]-Other-Data-Sources.md)