[⬅ Back to Database Fundamentals](../[0]-Introduction-to-Databases.md)

# Introduction to SQLite

SQLite is the world's most widely deployed database engine, even though most people never interact with it directly. It's not a server you install and connect to — it's a small C library that gets linked straight into an application, reading and writing an entire database to a single ordinary file on disk. That makes it the engine quietly running inside web browsers, mobile apps, and countless embedded devices. This Topic builds on the relational fundamentals from Database Fundamentals and shows how they look in SQLite: its file-based, serverless design, its unusually flexible type system, and the tools and PRAGMAs used to configure and tune it.

Download: [sqlite.org/download.html](https://www.sqlite.org/download.html)

## Table of Contents

**Getting Started**

   1. **[Getting Started With SQLite](./[1]-Getting-Started-With-SQLite.md)**
       1.1 What Is SQLite
       1.2 Installing And Connecting
       1.3 The sqlite3 CLI And Dot-Commands
       1.4 SQLite As An Embedded Database

**Data And Querying**

   2. **[Data Types And Table Creation In SQLite](./[2]-Data-Types-And-Table-Creation-In-SQLite.md)**
       2.1 Dynamic Typing And Type Affinity
       2.2 Creating Tables
       2.3 ROWID, INTEGER PRIMARY KEY, And WITHOUT ROWID
       2.4 STRICT Tables
   3. **[CRUD And Querying In SQLite](./[3]-CRUD-And-Querying-In-SQLite.md)**
       3.1 Inserting And Updating Data
       3.2 Selecting And Filtering
       3.3 UPSERT With ON CONFLICT
       3.4 SQLite-Specific Functions

**SQLite In Production**

   4. **[Indexes And Query Planning In SQLite](./[4]-Indexes-And-Query-Planning-In-SQLite.md)**
       4.1 Index Types In SQLite
       4.2 Creating And Using Indexes
       4.3 Reading EXPLAIN QUERY PLAN
       4.4 Common Performance Pitfalls
   5. **[Transactions, Concurrency, And PRAGMAs](./[5]-Transactions-Concurrency-And-PRAGMAs.md)**
       5.1 Transactions In SQLite
       5.2 Locking And The Single-Writer Model
       5.3 WAL Mode
       5.4 Useful PRAGMAs
   6. **[Backups, Extensions, And The SQLite Ecosystem](./[6]-Backups-Extensions-And-The-SQLite-Ecosystem.md)**
       6.1 Backing Up A SQLite Database
       6.2 Extensions: FTS5 And JSON1
       6.3 Tools And Ecosystem
       6.4 When To Choose SQLite
