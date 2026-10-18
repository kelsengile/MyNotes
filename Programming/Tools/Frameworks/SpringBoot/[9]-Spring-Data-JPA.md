[Previous](./[8]-Server-Side-Views-With-Thymeleaf.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[10]-Transactions-And-Migrations.md)

*Data*

# Lesson 9 - Spring Data JPA

**JPA (Jakarta Persistence API)** maps Java objects to relational database tables. **Hibernate** is the JPA implementation Boot uses. **Spring Data JPA** removes most of the repetitive data-access code on top.

### Setup

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>
```

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.h2.console.enabled=true
```

H2 is an in-memory database that is ideal for learning. For real projects, use PostgreSQL or MySQL with their drivers.

---

## 9.1 Entities

An **entity** is a class mapped to a table. Each instance is a row.

```java
@Entity
@Table(name = "books")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)   // database auto-increment
    private Long id;

    @Column(nullable = false, length = 200)
    private String title;

    @Column(nullable = false)
    private String author;

    private int year;

    @Enumerated(EnumType.STRING)                          // store the name, not the ordinal
    private Genre genre;

    protected Book() { }                                   // required by JPA

    public Book(String title, String author, int year, Genre genre) {
        this.title = title;
        this.author = author;
        this.year = year;
        this.genre = genre;
    }

    // getters (and setters where needed)
}

public enum Genre { FICTION, SCIENCE, HISTORY }
```

### Key rules

- Annotate with `@Entity` and provide an `@Id` field.
- Provide a **no-argument constructor** (it can be `protected`).
- Do not make the class `final`, because Hibernate creates proxies.
- Prefer `EnumType.STRING`. The default (`ORDINAL`) breaks if you reorder constants.

### Id generation strategies

| Strategy | Behavior |
|---|---|
| `IDENTITY` | Database auto-increment column |
| `SEQUENCE` | Database sequence (common with PostgreSQL) |
| `UUID` | Generates a UUID (use a `UUID` field type) |

### Schema control with `ddl-auto`

| Value | Effect |
|---|---|
| `none` | Do nothing |
| `validate` | Check that the schema matches the entities |
| `update` | Alter the schema automatically (risky; avoid in production) |
| `create-drop` | Create at start, drop at shutdown (good for tests and demos) |

For production, manage the schema with migrations instead (Lesson 10).

### Equality

Avoid using Lombok's `@Data` on entities. Its generated `equals`/`hashCode`/`toString` can trigger lazy loading or infinite loops with relationships.

---

## 9.2 Repositories

A **repository** is an interface. Spring Data generates the implementation at runtime.

```java
public interface BookRepository extends JpaRepository<Book, Long> { }
```

`JpaRepository<Book, Long>` means "entity type `Book`, id type `Long`". Without writing any implementation, you get:

| Method | Purpose |
|---|---|
| `save(entity)` | Insert or update |
| `findById(id)` | `Optional<Book>` |
| `findAll()` | All rows |
| `existsById(id)` | Boolean check |
| `count()` | Number of rows |
| `deleteById(id)` | Delete |
| `saveAll(list)` | Batch save |

### Using it

```java
@Service
public class BookService {

    private final BookRepository repo;

    public BookService(BookRepository repo) { this.repo = repo; }

    public Book add(Book book) { return repo.save(book); }

    public Book get(Long id) {
        return repo.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("Book", id));
    }

    public List<Book> all() { return repo.findAll(); }
}
```

### Paging and sorting

```java
Page<Book> page = repo.findAll(PageRequest.of(0, 10, Sort.by("title").ascending()));

page.getContent();        // the 10 rows
page.getTotalElements();  // total across all pages
page.getTotalPages();
```

A controller can accept `Pageable` directly: `public Page<Book> list(Pageable pageable)`. It reads `?page=0&size=10&sort=title,asc` automatically.

### Repository flavors

| Interface | Adds |
|---|---|
| `CrudRepository` | Basic CRUD |
| `ListCrudRepository` | CRUD returning `List` |
| `PagingAndSortingRepository` | Paging and sorting |
| `JpaRepository` | All of the above plus JPA-specific features (`flush`, batch deletes) |

---

## 9.3 Query methods and JPQL

### Derived queries (from the method name)

Spring parses the method name and builds the query:

```java
public interface BookRepository extends JpaRepository<Book, Long> {

    List<Book> findByAuthor(String author);
    List<Book> findByYearGreaterThan(int year);
    List<Book> findByTitleContainingIgnoreCase(String fragment);
    List<Book> findByGenreAndYearBetween(Genre genre, int from, int to);
    List<Book> findByAuthorOrderByYearDesc(String author);
    Optional<Book> findFirstByTitle(String title);
    boolean existsByTitle(String title);
    long countByGenre(Genre genre);
    void deleteByAuthor(String author);
}
```

| Keyword | Meaning |
|---|---|
| `And` / `Or` | Combine conditions |
| `Between` | Range |
| `LessThan` / `GreaterThan` | Comparisons |
| `Like` / `Containing` / `StartingWith` | Text matching |
| `In` | Value in a collection |
| `IsNull` / `IsNotNull` | Null checks |
| `OrderBy...Asc/Desc` | Sorting |
| `First` / `Top3` | Limit results |

If the method name becomes unreadable, switch to `@Query`.

### JPQL with `@Query`

**JPQL** queries entities and fields (not tables and columns):

```java
@Query("SELECT b FROM Book b WHERE b.author = :author AND b.year >= :since")
List<Book> recentByAuthor(@Param("author") String author, @Param("since") int since);

@Query("SELECT b FROM Book b WHERE LOWER(b.title) LIKE LOWER(CONCAT('%', :text, '%'))")
Page<Book> search(@Param("text") String text, Pageable pageable);
```

### Native SQL

```java
@Query(value = "SELECT * FROM books WHERE year > :y", nativeQuery = true)
List<Book> nativeQuery(@Param("y") int y);
```

### Modifying queries

```java
@Modifying
@Transactional
@Query("UPDATE Book b SET b.genre = :genre WHERE b.author = :author")
int updateGenre(@Param("author") String author, @Param("genre") Genre genre);
```

### Projections (fetch only some fields)

```java
public record BookSummary(String title, String author) { }

List<BookSummary> findByGenre(Genre genre);       // Spring selects only these columns
```

Always use **named parameters** (`:name`) and never concatenate user input into queries, to prevent SQL injection.

---

## 9.4 Relationships

### Many-to-one and one-to-many

An author has many books; each book has one author.

```java
@Entity
public class Author {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Book> books = new ArrayList<>();

    public void addBook(Book book) {
        books.add(book);
        book.setAuthor(this);               // keep both sides in sync
    }
}

@Entity
public class Book {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "author_id")          // foreign key column in the books table
    private Author author;

    void setAuthor(Author a) { this.author = a; }
}
```

### Vocabulary

| Term | Meaning |
|---|---|
| **Owning side** | The side holding the foreign key (`Book.author`) |
| **`mappedBy`** | Marks the inverse side, which does not own the column |
| **`cascade`** | Propagates operations (save, delete) to children |
| **`orphanRemoval`** | Deletes children removed from the collection |

### Many-to-many

```java
@Entity
public class Student {
    @ManyToMany
    @JoinTable(name = "student_course",
               joinColumns = @JoinColumn(name = "student_id"),
               inverseJoinColumns = @JoinColumn(name = "course_id"))
    private Set<Course> courses = new HashSet<>();
}
```

For extra data on the link (such as an enrollment date), model the join table as its own entity.

### Fetching: lazy vs eager

- `LAZY` — related data loads **only when accessed**. This is the recommended default.
- `EAGER` — loads immediately, often fetching far more than needed.

`@OneToMany` and `@ManyToMany` are lazy by default; `@ManyToOne` and `@OneToOne` are **eager** by default, so set `fetch = LAZY` explicitly.

### The N+1 problem

Loading 100 authors and then touching each author's `books` runs **1 + 100** queries.

Fixes:

```java
@Query("SELECT a FROM Author a JOIN FETCH a.books")
List<Author> findAllWithBooks();

@EntityGraph(attributePaths = "books")
List<Author> findAll();
```

Turn on `spring.jpa.show-sql=true` during development and watch the query count.

### Lazy loading outside a transaction

Accessing a lazy field after the transaction ends throws `LazyInitializationException`. Fix it by fetching what you need inside the service (join fetch or entity graph) and returning DTOs rather than entities.

### Serialization warning

Returning bidirectional entities directly as JSON can loop forever (author → books → author ...). Return DTOs instead (Lesson 6).

[Previous](./[8]-Server-Side-Views-With-Thymeleaf.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[10]-Transactions-And-Migrations.md)