[Previous](./[3]-Database-Management-Systems.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[5]-Schemas-And-Data-Types.md)

*Relational Design*

# Lesson 4 - Tables, Rows, And Columns

## 4.1 The Table as a Structure

In a relational database, data is organized into **tables**. Each table stores information about one type of entity — for example, a `users` table stores users, and an `orders` table stores orders. A table is made up of **columns** (which define what kind of data is stored) and **rows** (which hold the actual data, one row per record).

Think of a table like a single sheet in a spreadsheet, but with strict rules about what can go in each column.

---

## 4.2 Rows (Records)

A **row**, also called a **record** or **tuple**, represents one single instance of the entity the table describes. In a `users` table, each row is one specific user. Every row has a value (or `NULL`, see [1.4](./[5]-Schemas-And-Data-Types.md)) for every column defined on the table.

---

## 4.3 Columns (Fields) and Attributes

A **column**, also called a **field** or **attribute**, defines one piece of information tracked for every row — for example, `first_name`, `email`, or `created_at`. Every column has a defined **data type** (covered in [Lesson 5](./[5]-Schemas-And-Data-Types.md)) that constrains what kind of values it can hold.

---

## 4.4 A Simple Example Table

Here's a small `users` table to make the terminology concrete:

| id | first_name | email | created_at |
|---|---|---|---|
| 1 | Ana | ana@example.com | 2026-01-04 |
| 2 | Ben | ben@example.com | 2026-02-11 |
| 3 | Cleo | cleo@example.com | 2026-03-30 |

- `id`, `first_name`, `email`, and `created_at` are the **columns**.
- Each of the three lines below the header is a **row**.
- The whole structure is the `users` **table**.

[Previous](./[3]-Database-Management-Systems.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[5]-Schemas-And-Data-Types.md)
