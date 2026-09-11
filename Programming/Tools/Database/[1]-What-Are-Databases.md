[Previous](./[0]-Introduction-to-Databases.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[2]-Types-Of-Databases.md)

*Basics*

# Lesson 1 - What Are Databases

## 1.1 Defining a Database

A **database** is an organized collection of data that is stored electronically and structured so it can be accessed, managed, and updated efficiently. The word "organized" is doing a lot of work in that definition — a folder full of random text files technically holds data, but it isn't a database, because there's no consistent structure or system managing how that data is stored and retrieved.

In practice, a database is almost always paired with software that manages it, called a **Database Management System (DBMS)**. When people say "database," they often mean the combination of the data itself and the system that manages it.

---

## 1.2 Databases vs Flat Files and Spreadsheets

Before reaching for a database, many people store data in flat files (like `.csv` or `.txt`) or spreadsheets. These work fine at a small scale, but they break down as data grows:

- **No enforced structure** — nothing stops one row from having a date in one format and another row using a different format.
- **No safe concurrent access** — two people editing the same spreadsheet at once can overwrite each other's changes.
- **Slow lookups at scale** — finding one row in a million-row spreadsheet means scanning the whole file; databases use structures like indexes to avoid this.
- **No relationships** — spreadsheets don't have a built-in way to link a customer to their orders without duplicating data.

Databases solve each of these problems directly, which is why they become necessary as soon as data needs to be shared, trusted, or queried at scale.

---

## 1.3 Why Databases Exist

Databases exist to solve a handful of recurring problems that come up the moment more than one person or program needs to read and write the same data:

- **Persistence** — data needs to survive after the program that created it stops running.
- **Sharing** — multiple users or applications often need access to the same data at the same time.
- **Integrity** — data needs rules (like "an email must be unique") that are enforced consistently, not just hoped for.
- **Scale** — data needs to remain fast to search and update even as it grows from hundreds to billions of rows.

---

## 1.4 Key Benefits

Using a proper database instead of ad-hoc storage gives an application several concrete benefits:

- **Structure** — a defined shape for the data (tables, fields, types) that keeps it consistent.
- **Speed** — specialized storage and indexing make lookups dramatically faster than scanning raw files.
- **Reliability** — features like transactions (covered in [Lesson 12](./[12]-Transactions-And-ACID.md)) protect data even if something fails mid-operation.
- **Concurrency** — many users or programs can safely read and write at the same time without corrupting data.
- **Security** — fine-grained control over who can see or change which pieces of data.

[Previous](./[0]-Introduction-to-Databases.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[2]-Types-Of-Databases.md)
