[⬅ Back to README](../../../README.md)

# SQL - Structured Query Language


Welcome! This is a self-paced course for learning SQL, the standard language used to store, query, and manage data in relational databases.

---

## What is SQL?

SQL lets you:
- Store structured data in tables made up of rows and columns
- Query and filter data to answer specific questions
- Combine data from multiple tables using joins
- Insert, update, and delete records
- Enforce rules about what data is allowed (constraints, keys, types)
- Aggregate and summarize large datasets

## Table of Contents

**Getting Started**  
    1. **[Installing a Database & First-Time Setup](./[1]-Installation-and-Setup.md)**  
       1.1 Why start here?  
       1.2 Choosing a database for learning  
       1.3 Option A: SQLite (recommended for this course)  
       1.4 Option B: A GUI tool (optional but helpful)  
       1.5 Option C: Practice online, no installation  
       1.6 Useful SQLite command-line shortcuts  
       1.7 Looking ahead  
    2. **[SQL Basics: SELECT, FROM, WHERE](./[2]-SQL-Basics.md)**  
       2.1 Setting up sample data  
       2.2 The anatomy of a query  
       2.3 SELECT: choosing columns  
       2.4 FROM: choosing the table  
       2.5 WHERE: filtering rows  
       2.6 Query clause order  
       2.7 Comments  
       2.8 A note on case sensitivity  
    3. **[Filtering & Sorting Data](./[3]-Filtering-and-Sorting.md)**    
       3.1 Combining conditions: AND, OR, NOT  
       3.2 IN: matching a list of values  
       3.3 BETWEEN: matching a range  
       3.4 LIKE: pattern matching text  
       3.5 IS NULL: checking for missing values  
       3.6 ORDER BY: sorting results  
       3.7 LIMIT and OFFSET: controlling how many rows come back  
       3.8 DISTINCT: removing duplicate rows  
       3.9 Putting it together  

**Tables & Data**  
    4. **[Data Types & Table Design](./[4]-Data-Types-and-Table-Design.md)**  
       4.1 Why types matter  
       4.2 Common data type categories  
       4.3 SQLite's flexible typing  
       4.4 Designing a table: thinking in entities  
       4.5 Choosing appropriate types, column by column  
       4.6 One fact, one column  
       4.7 Nullable vs required columns  
    5. **[Creating Tables: CREATE, ALTER, DROP](./[5]-Creating-Tables.md)**  
       5.1 CREATE TABLE  
       5.2 ALTER TABLE  
       5.3 DROP TABLE  
       5.4 TRUNCATE TABLE  
       5.5 A full example: building the bookstore schema properly  
       5.6 Viewing a table's structure  
    6. **[Inserting, Updating & Deleting Data](./[6]-Insert-Update-Delete.md)**  
       6.1 INSERT: adding rows  
       6.2 UPDATE: changing existing rows  
       6.3 DELETE: removing rows  
       6.4 A safety habit: SELECT before you UPDATE or DELETE  
       6.5 Transactions: an early preview  
       6.6 RETURNING (PostgreSQL, SQLite 3.35+)  
       6.7 UPSERT: insert, or update if it already exists  
    7. **[Handling NULLs](./[7]-Handling-NULLs.md)**  
       7.1 What NULL means  
       7.2 Why NULL = NULL doesn't work  
       7.3 NULL in calculations  
       7.4 NULL in AND / OR logic  
       7.5 COALESCE: substituting a default value  
       7.6 NULLIF: turning a value into NULL conditionally  
       7.7 NULLs and aggregate functions  
       7.8 NULLs and sorting  
       7.9 NULLs and UNIQUE constraints  

**Functions & Aggregation**  
    8. **[String, Date & Math Functions and CASE Expressions](./[8]-Functions-and-CASE-Expressions.md)**  
       8.1 String functions  
       8.2 Math functions  
       8.3 Date and time functions  
       8.4 Type conversion: CAST  
       8.5 CASE expressions: conditional logic inside a query  
    9. **[Aggregate Functions & GROUP BY](./[9]-Aggregate-Functions-and-Group-By.md)**  
       9.1 The five core aggregate functions  
       9.2 GROUP BY: aggregating per category  
       9.3 The golden rule of GROUP BY  
       9.4 Grouping by multiple columns  
       9.5 HAVING: filtering groups  
       9.6 Clause execution order (conceptual)  
       9.7 Combining WHERE and HAVING  
       9.8 COUNT(DISTINCT ...)  
       9.9 A full example  

**Combining Data**  
    10. **[Set Operations: UNION, INTERSECT & EXCEPT](./[10]-Set-Operations.md)**  
        10.1 The requirement: compatible columns  
        10.2 Sample setup  
        10.3 UNION: combine rows, remove duplicates  
        10.4 UNION ALL: combine rows, keep duplicates  
        10.5 INTERSECT: only rows in both  
        10.6 EXCEPT (aka MINUS): rows in the first, not the second  
        10.7 Ordering the combined result  
        10.8 A practical use: combining data from similarly-shaped tables  
        10.9 Set operations vs JOIN: when to use which  
    11. **[Joins: INNER, LEFT, RIGHT & FULL](./[11]-Joins.md)**  
        11.1 Sample setup  
        11.2 INNER JOIN: only matching rows  
        11.3 LEFT JOIN (LEFT OUTER JOIN): all rows from the left, matched or not  
        11.4 RIGHT JOIN (RIGHT OUTER JOIN): all rows from the right, matched or not  
        11.5 FULL JOIN (FULL OUTER JOIN): everything, matched or not  
        11.6 Visual summary  
        11.7 Joining more than two tables  
        11.8 CROSS JOIN: every combination  
        11.9 Self joins  
    12. **[Subqueries & Nested Queries](./[12]-Subqueries.md)**  
        12.1 Scalar subqueries: a single value  
        12.2 Subqueries with IN  
        12.3 Subqueries with comparison operators + ANY/ALL  
        12.4 EXISTS: checking for any matching row  
        12.5 Correlated vs uncorrelated subqueries  
        12.6 Subqueries in FROM (derived tables)  
        12.7 Subqueries vs JOINs vs CTEs  
        12.8 Subqueries in UPDATE and DELETE  

**Data Integrity & Design**  
    13. **[Keys & Relationships: Primary and Foreign Keys](./[13]-Keys-and-Relationships.md)**  
        13.1 Primary keys  
        13.2 Foreign keys  
        13.3 Relationship types  
        13.4 ON DELETE and ON UPDATE behavior  
        13.5 Why keys matter  
    14. **[Constraints & Data Integrity](./[14]-Constraints-and-Data-Integrity.md)**  
        14.1 NOT NULL  
        14.2 UNIQUE  
        14.3 CHECK  
        14.4 DEFAULT  
        14.5 PRIMARY KEY and FOREIGN KEY  
        14.6 Naming constraints  
        14.7 Adding constraints to an existing table  
        14.8 Dropping constraints  
        14.9 What happens when a constraint is violated?  
        14.10 Why enforce integrity in the database, not just the application?  
    15. **[Normalization & Schema Design](./[15]-Normalization-and-Schema-Design.md)**  
        15.1 Entity-Relationship (ER) modeling  
        15.2 Normalization  
        15.3 The problem: a denormalized table  
        15.4 Normal forms  
        15.5 Denormalization: sometimes on purpose  
        15.6 Practical schema design process  

**Advanced Querying**  
    16. **[Views](./[16]-Views.md)**  
        16.1 Creating a view  
        16.2 Why use views?  
        16.3 Updating through a view  
        16.4 Dropping and replacing a view  
        16.5 Materialized views  
        16.6 Views vs CTEs (preview of Lesson 19)  
    17. **[Indexes & Query Performance](./[17]-Indexes-and-Performance.md)**  
        17.1 The problem indexes solve  
        17.2 Creating an index  
        17.3 How indexes work (conceptually)  
        17.4 Primary keys and unique constraints are automatically indexed  
        17.5 Composite (multi-column) indexes  
        17.6 Checking whether a query uses an index: EXPLAIN  
        17.7 When an index helps  
        17.8 When an index doesn't help (or actively hurts)  
        17.9 Types of indexes (brief overview)  
        17.10 Dropping an index  
        17.11 A practical mental model  
    18. **[Transactions & ACID Properties](./[18]-Transactions-and-ACID.md)**  
        18.1 What a transaction is  
        18.2 The classic example: transferring money  
        18.3 BEGIN, COMMIT, ROLLBACK  
        18.4 Autocommit mode  
        18.5 The ACID properties  
        18.6 Isolation levels  
        18.7 SAVEPOINT: partial rollback within a transaction  
        18.8 Deadlocks  
        18.9 When to use explicit transactions  
    19. **[Common Table Expressions (CTEs) & Recursive Queries](./[19]-CTEs-and-Recursive-Queries.md)**  
        19.1 What a CTE is  
        19.2 Why use a CTE instead of a subquery?  
        19.3 Multiple CTEs in one query  
        19.4 Referencing a CTE more than once  
        19.5 Recursive CTEs  
        19.6 Avoiding infinite recursion  
        19.7 CTEs in INSERT/UPDATE/DELETE  
    20. **[Window Functions](./[20]-Window-Functions.md)**  
        20.1 The problem window functions solve  
        20.2 Basic syntax: OVER()  
        20.3 PARTITION BY: windows within groups  
        20.4 Ranking functions  
        20.5 A practical use of ranking: "top N per group"  
        20.6 Offset functions: LAG() and LEAD()  
        20.7 Running totals with window frames  
        20.8 FIRST_VALUE() and LAST_VALUE()  
        20.9 Window functions vs GROUP BY: when to use which  

**Programmability & Administration**  
    21. **[Stored Procedures & Functions](./[21]-Stored-Procedures-and-Functions.md)**  
        21.1 What they are  
        21.2 A simple function (PostgreSQL)  
        21.3 A simple function (MySQL)  
        21.4 A stored procedure (PostgreSQL)  
        21.5 Procedural control flow  
        21.6 Parameters: IN, OUT, INOUT  
        21.7 Why use stored procedures/functions?  
        21.8 Trade-offs and criticisms  
        21.9 Dropping procedures/functions  
    22. **[Triggers](./[22]-Triggers.md)**  
        22.1 What a trigger is  
        22.2 When triggers fire  
        22.3 A trigger example (PostgreSQL)  
        22.4 OLD and NEW  
        22.5 A trigger example (SQLite)  
        22.6 A trigger example (MySQL)  
        22.7 Using BEFORE triggers to validate/modify data  
        22.8 Common real-world uses for triggers  
        22.9 Trade-offs of triggers  
        22.10 Dropping a trigger  
    23. **[User Permissions & Security](./[23]-Permissions-and-Security.md)**  
        23.1 Users and roles  
        23.2 GRANT: giving permissions  
        23.3 REVOKE: taking permissions away  
        23.4 Roles as permission groups  
        23.5 The principle of least privilege  
        23.6 Using views to restrict access (recap of Lesson 16)  
        23.7 Row-level security (PostgreSQL, SQL Server)  
        23.8 SQL injection: the most important security concept for SQL  
        23.9 Encryption  

**SQL in Practice**  
    24. **[Importing & Exporting Data](./[24]-Importing-and-Exporting-Data.md)**  
        24.1 Importing CSV data  
        24.2 Exporting to CSV  
        24.3 Working with JSON  
        24.4 Dump and restore (full database backups)  
        24.5 Practical import checklist  
    25. **[SQL Dialects: PostgreSQL, MySQL, SQLite & SQL Server](./[25]-SQL-Dialects.md)**  
        25.1 The four databases at a glance  
        25.2 Where dialects diverge — a consolidated reference  
        25.3 Why does this variation exist?  
        25.4 Practical advice for working across dialects  
        25.5 Choosing a database for a new project (a rough guide)  
    26. **[Query Optimization & Execution Plans](./[26]-Query-Optimization.md)**  
        26.1 How a query actually runs  
        26.2 EXPLAIN: seeing the plan  
        26.3 Reading a plan: key things to look for  
        26.4 Common causes of slow queries, and their fixes  
        26.5 N+1 query problems  
        26.6 Query rewriting techniques  
        26.7 A practical optimization workflow  
        26.8 You've completed the course! 🎉  