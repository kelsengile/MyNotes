[⬅ Back to README](../../../README.md)

# Introduction to Databases

A database is an organized collection of data that can be stored, retrieved, updated, and managed reliably over time. Databases sit underneath almost every application you use — they remember your login, hold your shopping cart, store every message you've ever sent. This Topic builds up the fundamental, tool-agnostic concepts behind databases from the ground up: what they are, how data is modeled and queried, how systems keep that data safe and fast, and how databases scale in the real world.

These lessons intentionally avoid tying the concepts to any single product. Once you understand the fundamentals here, head over to the tool-specific folders below to see how those concepts are put into practice with real, widely-used tools.

## Table of Contents

**Basics**
   1. **[What Are Databases?](./[1]-What-Are-Databases.md)**  
       1.1 Defining a Database  
       1.2 Databases vs Flat Files and Spreadsheets  
       1.3 Why Databases Exist  
       1.4 Key Benefits  
   2. **[Types Of Databases](./[2]-Types-Of-Databases.md)**  
       2.1 Relational Databases  
       2.2 NoSQL Databases  
       2.3 Other Database Types  
       2.4 Comparing Use Cases  
   3. **[Database Management Systems](./[3]-Database-Management-Systems.md)**  
       3.1 What Is a DBMS  
       3.2 Core Responsibilities of a DBMS  
       3.3 Client-Server Architecture  
       3.4 Popular DBMS Examples  

**Relational Design**

   4. **[Tables, Rows, And Columns](./[4]-Tables-Rows-And-Columns.md)**  
       4.1 The Table as a Structure  
       4.2 Rows (Records)  
       4.3 Columns (Fields) and Attributes  
       4.4 A Simple Example Table  
   5. **[Schemas And Data Types](./[5]-Schemas-And-Data-Types.md)**  
       5.1 What Is a Schema  
       5.2 Common Data Types  
       5.3 Constraints  
       5.4 NULL Values  
   6. **[Keys And Relationships](./[6]-Keys-And-Relationships.md)**  
       6.1 Primary Keys  
       6.2 Foreign Keys  
       6.3 One-to-Many, Many-to-Many, and One-to-One  
       6.4 Entity-Relationship Diagrams  
   7. **[Normalization](./[7]-Normalization.md)**  
       7.1 Why Normalize  
       7.2 First Normal Form (1NF)  
       7.3 Second Normal Form (2NF)  
       7.4 Third Normal Form (3NF)  
       7.5 Denormalization Trade-offs  

**SQL And Querying**

   8. **[Introduction To SQL](./[8]-Introduction-To-SQL.md)**  
       8.1 What Is SQL  
       8.2 SQL Sublanguages (DDL, DML, DQL, DCL)  
       8.3 Basic Syntax Rules  
       8.4 Your First Query  
   9. **[CRUD Operations](./[9]-CRUD-Operations.md)**  
       9.1 Create (INSERT)  
       9.2 Read (SELECT)  
       9.3 Update (UPDATE)  
       9.4 Delete (DELETE)  
   10. **[Filtering, Sorting, And Aggregating](./[10]-Filtering-Sorting-And-Aggregating.md)**  
       10.1 WHERE Clauses  
       10.2 ORDER BY and LIMIT  
       10.3 Aggregate Functions  
       10.4 GROUP BY and HAVING  
   11. **[Joins And Subqueries](./[11]-Joins-And-Subqueries.md)**  
       11.1 Why Joins Are Needed  
       11.2 INNER JOIN  
       11.3 OUTER JOINs (LEFT/RIGHT/FULL)  
       11.4 Subqueries  

**Transactions And Performance**

   12. **[Transactions And ACID](./[12]-Transactions-And-ACID.md)**  
       12.1 What Is a Transaction  
       12.2 Atomicity  
       12.3 Consistency, Isolation, and Durability  
       12.4 Transaction Syntax Example  
   13. **[Indexing And Query Performance](./[13]-Indexing-And-Query-Performance.md)**  
       13.1 What Is an Index  
       13.2 How Indexes Speed Up Reads  
       13.3 Trade-offs of Indexing  
       13.4 Reading a Query Execution Plan  
   14. **[Concurrency And Locking](./[14]-Concurrency-And-Locking.md)**  
       14.1 The Concurrency Problem  
       14.2 Locking Strategies  
       14.3 Isolation Levels  
       14.4 Deadlocks  

**Beyond Relational**

   15. **[NoSQL Database Types](./[15]-NoSQL-Database-Types.md)**  
       15.1 Document Databases  
       15.2 Key-Value Stores  
       15.3 Column-Family Databases  
       15.4 Graph Databases  
   16. **[CAP Theorem And Consistency Models](./[16]-CAP-Theorem-And-Consistency-Models.md)**  
       16.1 What Is the CAP Theorem  
       16.2 Consistency vs Availability vs Partition Tolerance  
       16.3 Strong vs Eventual Consistency  
       16.4 Choosing a Consistency Model  

**Operations And Next Steps**

   17. **[Database Administration Basics](./[17]-Database-Administration-Basics.md)**  
       17.1 Users, Roles, and Permissions  
       17.2 Backups and Restores  
       17.3 Migrations and Schema Changes  
       17.4 Monitoring and Maintenance  
   18. **[Scaling Databases](./[18]-Scaling-Databases.md)**  
       18.1 Vertical vs Horizontal Scaling  
       18.2 Replication  
       18.3 Sharding and Partitioning  
       18.4 Caching Layers  
   19. **[Choosing A Database Tool](./[19]-Choosing-A-Database-Tool.md)**  
       19.1 Matching Tools to Tasks  
       19.2 SQL Databases for Structured Data  
       19.3 NoSQL Databases for Flexible Data  
       19.4 Where to Go Next  

## Database Tools

The lessons above teach the fundamentals that apply across the whole database ecosystem. To see those fundamentals applied to specific, industry-standard tools, continue on to:

- **[MongoDB](./MongoDB/[0]-Introduction-to-MongoDB.md)** — a widely-used document-oriented NoSQL database; a strong starting point for learning schema-flexible, document-based databases hands-on.

- **[MySQL](./MySQL/[0]-Introduction-to-MySQL.md)** — a widely-used open-source relational database; a strong starting point for learning SQL databases hands-on.

- **[PostgreSQL](./PostgreSQL/[0]-Introduction-to-PostgreSQL.md)** — a widely-used open-source relational database; a strong starting point for learning SQL databases hands-on.

- **[Redis](./Redis/[0]-Introduction-to-Redis.md)** — an in-memory data store commonly used for caching, fast data access, and real-time applications.

- **[SQLite](./SQLite/[0]-Introduction-to-SQLite.md)** — a lightweight, serverless relational database that is useful for embedded applications, local storage, and smaller projects.
