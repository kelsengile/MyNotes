[⬅ Back to Database Fundamentals](../[0]-Introduction-to-Databases.md)

# Introduction to PostgreSQL

PostgreSQL (often called "Postgres") is a powerful, open-source relational database known for strictly following the SQL standard while also extending it with advanced features rarely found elsewhere — native arrays, a rich JSON type, full-text search, and a plugin architecture that lets it be extended into new kinds of database entirely. This Topic builds on the relational fundamentals from Database Fundamentals and shows how they're expressed in PostgreSQL, along with the features that make it a favorite for developers who need more than basic tables and columns.

Download: [postgresql.org/download](https://www.postgresql.org/download/)

## Table of Contents

**Getting Started**

   1. **[Getting Started With PostgreSQL](./[1]-Getting-Started-With-PostgreSQL.md)**  
       1.1 What Is PostgreSQL  
       1.2 Installing And Connecting  
       1.3 psql Basics  
       1.4 Databases, Schemas, And Roles  

**Data And Querying**

   2. **[Data Types And Table Creation In PostgreSQL](./[2]-Data-Types-And-Table-Creation-In-PostgreSQL.md)**  
       2.1 Standard And Advanced Data Types  
       2.2 Arrays And JSONB  
       2.3 Creating Tables And Constraints  
       2.4 SERIAL vs IDENTITY  
   3. **[Querying In PostgreSQL](./[3]-Querying-In-PostgreSQL.md)**  
       3.1 SELECT And Filtering  
       3.2 Joins And CTEs  
       3.3 Window Functions  
       3.4 Views And Materialized Views  

**Advanced PostgreSQL**

   4. **[Indexes And Query Planning](./[4]-Indexes-And-Query-Planning.md)**  
       4.1 Index Types (B-tree, GIN, GiST, BRIN)  
       4.2 Creating Indexes  
       4.3 EXPLAIN ANALYZE  
       4.4 Query Tuning Tips  
   5. **[Transactions And MVCC](./[5]-Transactions-And-MVCC.md)**  
       5.1 Transactions In PostgreSQL  
       5.2 MVCC Explained  
       5.3 Isolation Levels  
       5.4 VACUUM And Bloat  
   6. **[Roles, Extensions, And The Ecosystem](./[6]-Roles-Extensions-And-The-Ecosystem.md)**  
       6.1 Roles And Permissions  
       6.2 Popular Extensions  
       6.3 Replication And High Availability  
       6.4 When To Choose PostgreSQL 