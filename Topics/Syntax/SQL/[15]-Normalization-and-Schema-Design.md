# Lesson 15: ER Modeling, Normalization & Schema Design

---

Before writing a single `CREATE TABLE` statement, it helps to model your data conceptually first. This lesson covers **entity-relationship (ER) modeling** — the design step that happens before the schema — and then **normalization**, the discipline for organizing tables well once you start building them.

## Entity-Relationship (ER) modeling

An **entity-relationship diagram (ERD)** is a visual way of planning a database's structure before writing SQL. It forces you to think through what the real-world "things" are, what facts you need about them, and how they connect — catching design mistakes on paper (or a whiteboard) instead of after the tables already exist.

### Entities and attributes

An **entity** is a distinct "thing" your database needs to track — usually a noun in your domain: `Author`, `Book`, `Customer`, `Order`. Each entity becomes (in most cases) one table.

An **attribute** is a single fact about an entity — usually a column: `Author` has `name` and `country`; `Book` has `title`, `price`, and `published_year`.

Every entity should have a way to uniquely identify each instance of it — this becomes its primary key (Lesson 13).

### Relationships and cardinality

A **relationship** describes how two entities are connected — `Author` *writes* `Book`. Relationships have a **cardinality**, describing how many instances of one entity can relate to how many of another:

| Cardinality | Meaning | Example |
|---|---|---|
| One-to-one (1:1) | One row in A relates to at most one row in B, and vice versa | `User` — `UserProfile` |
| One-to-many (1:N) | One row in A relates to many rows in B, but each row in B relates to only one row in A | `Author` — `Book` |
| Many-to-many (M:N) | Many rows in A can relate to many rows in B | `Student` — `Course` |

These map directly onto the foreign-key patterns covered in Lesson 13: 1:N is a plain foreign key on the "many" side; M:N requires a junction table; 1:1 is a foreign key with an added `UNIQUE` constraint.

### A simple ERD, sketched in text

Since drawing tools vary, here's the bookstore schema described in a common ERD shorthand — `||` means "exactly one," `o{` means "zero or many":

```
AUTHORS ||--o{ BOOKS : "writes"
CUSTOMERS ||--o{ ORDERS : "places"
ORDERS ||--o{ ORDER_ITEMS : "contains"
BOOKS ||--o{ ORDER_ITEMS : "appears in"
```

Read the first line as: "one author writes zero or many books." This single line already tells you `books` will need an `author_id` foreign key pointing back to `authors` — the ERD and the eventual schema are two views of the same design.

In practice, most people sketch ERDs with an actual diagramming tool (dbdiagram.io, draw.io, Lucidchart, or a GUI database tool's built-in schema visualizer) rather than text notation — but the underlying thinking is identical either way, and pencil-and-paper works fine for learning.

### Why model before building

Sketching entities, attributes, and relationships first — before typing `CREATE TABLE` — surfaces questions early that are expensive to fix later: *Can a book really have only one author, or do I need a junction table for co-authors? Does a customer need to exist before their first order, or can I allow guest checkouts?* Answering these on a whiteboard is far cheaper than migrating a live production schema after the fact.

## Normalization

Once you have an ER model, normalization is the process of organizing the resulting tables to reduce redundancy and prevent certain kinds of inconsistency. It's less about memorizing rules and more about a way of thinking: *each fact should be stored in exactly one place.*

## The problem: a denormalized table

Imagine one big table instead of separate `books` and `authors` tables:

| book_id | title | author_name | author_country | price |
|---|---|---|---|---|
| 1 | The Left Hand of Darkness | Ursula K. Le Guin | USA | 9.99 |
| 6 | The Dispossessed | Ursula K. Le Guin | USA | 9.50 |

Le Guin's name and country are duplicated across every one of her books. This causes three classic problems:

- **Update anomaly**: if Le Guin's listed country needs correcting, you must remember to update *every row* that mentions her — miss one, and your data is now inconsistent with itself.
- **Insertion anomaly**: you can't record a new author until they have at least one book, since there's no separate place to store author-only information.
- **Deletion anomaly**: if you delete her only remaining book, you lose all record of the author entirely, even though that wasn't your intent.

Splitting into `authors` and `books` tables (as we've done throughout this course) — with a foreign key connecting them — solves all three: the author's country is stored once, authors can exist without books, and deleting a book doesn't erase the author.

## Normal forms

Normalization is formalized as a series of "normal forms," each building on the last.

### First Normal Form (1NF)

- Each column holds a single, atomic value — no lists or repeated groups crammed into one field.
- Each row is unique (usually enforced via a primary key).

Violates 1NF:
| order_id | items |
|---|---|
| 1 | "Book A, Book B, Book C" |

Fixes it by giving each item its own row in a separate table:
| order_id | item |
|---|---|
| 1 | Book A |
| 1 | Book B |
| 1 | Book C |

### Second Normal Form (2NF)

- Must satisfy 1NF.
- Every non-key column must depend on the *whole* primary key, not just part of it. (Only relevant for tables with composite primary keys.)

Violates 2NF — `course_name` depends only on `course_id`, not on the full `(student_id, course_id)` key:
| student_id | course_id | course_name |
|---|---|---|
| 1 | 101 | Intro to SQL |
| 2 | 101 | Intro to SQL |

Fix: move `course_name` into its own `courses` table, keyed by `course_id` alone.

### Third Normal Form (3NF)

- Must satisfy 2NF.
- No non-key column depends on *another non-key column* (this is called a "transitive dependency").

Violates 3NF — `country_code` depends on `author_country`, not directly on `author_id`:
| author_id | author_country | country_code |
|---|---|---|
| 1 | USA | +1 |
| 2 | Japan | +81 |

Fix: move country info (`country_code`) into a separate `countries` table, referenced by `author_country`.

### Beyond 3NF

Further normal forms exist (BCNF, 4NF, 5NF) for edge cases involving more complex dependency patterns, but 3NF is where most real-world schema design stops — it eliminates the vast majority of practical redundancy problems.

## Denormalization: sometimes on purpose

Normalization optimizes for data integrity and minimal redundancy — but every join has a performance cost (Lesson 26). For read-heavy systems like reporting dashboards or analytics warehouses, it's common to deliberately **denormalize**: duplicate some data back into wider tables to avoid expensive joins at query time.

This is a genuine trade-off, not a mistake:
- **Normalized**: less redundancy, easier to keep consistent, but more joins needed to answer questions
- **Denormalized**: faster to read, but risk of the same update/insertion/deletion anomalies normalization was designed to prevent

A common real-world pattern: keep the "source of truth" data normalized (e.g., in the main application database), but periodically copy it into a denormalized form for reporting (e.g., in a data warehouse), where read speed matters more than write consistency.

## Practical schema design process

1. **Identify entities** — the "nouns" in your domain (author, book, customer, order).
2. **Identify attributes** for each entity — facts that belong to exactly that entity.
3. **Identify relationships and cardinality** between entities — one-to-many, many-to-many, one-to-one (see ER modeling, above, and Lesson 13 for how these become foreign keys).
4. **Draw it out** — an ERD, even a rough sketch on paper, catches design problems before you write a single `CREATE TABLE`.
5. **Apply normalization**, at least to 3NF, unless you have a specific, deliberate reason to denormalize.
6. **Add constraints** (Lesson 14) to enforce the rules you've identified.

---

## Exercises

1. This table violates 1NF. Redesign it:

| order_id | customer_name | products |
|---|---|---|
| 1 | Amara | "Pen, Notebook, Eraser" |

2. This table violates 3NF (`department_budget` depends on `department`, not on `employee_id`). Redesign it:

| employee_id | name | department | department_budget |
|---|---|---|---|
| 1 | Beto | Sales | 500000 |
| 2 | Chidi | Sales | 500000 |

3. Give one realistic scenario where deliberately denormalizing data would be a reasonable choice.
4. For a food delivery app with `Restaurant`, `Menu Item`, `Customer`, and `Order` entities: identify the cardinality of the relationship between `Restaurant` and `Menu Item`, and between `Order` and `Menu Item`. Which one needs a junction table?
5. Sketch (in the `||--o{` text notation used in this lesson) the relationship between `Restaurant` and `Menu Item` from exercise 4.

### Answers

```
1. Split into two tables:
   orders(order_id, customer_name)
   order_items(order_id, product)
   — with one row per product per order in order_items.

2. Split into two tables:
   employees(employee_id, name, department)
   departments(department, budget)
   — department_budget now lives in exactly one place.

3. A reporting dashboard that needs to show "total sales by region" for
   millions of rows, run many times a day — pre-joining and storing
   region alongside each sale avoids repeating an expensive join on
   every single dashboard refresh, at the cost of needing to keep that
   duplicated region value in sync if it ever changes.

4. Restaurant to Menu Item is one-to-many (one restaurant has many menu
   items, but each menu item belongs to exactly one restaurant) — a
   plain foreign key (menu_item.restaurant_id) is enough, no junction
   table needed. Order to Menu Item is many-to-many (one order can
   contain many menu items, and the same menu item can appear on many
   different orders) — this DOES need a junction table, typically
   called order_items, to record which items (and quantities) belong
   to which order.

5. RESTAURANT ||--o{ MENU_ITEM : "offers"
```
