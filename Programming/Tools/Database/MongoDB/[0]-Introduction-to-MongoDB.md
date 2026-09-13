[⬅ Back to Database Fundamentals](../[0]-Introduction-to-Databases.md)

# Introduction to MongoDB

MongoDB is the most widely used **document database**, storing data as flexible, JSON-like documents instead of rigid rows and columns. It's built for applications where the shape of the data changes over time, where nested and hierarchical structures are common, and where developers want their database records to look a lot like the objects their code already works with. This Topic applies the NoSQL concepts introduced in Database Fundamentals directly to MongoDB — its documents, queries, aggregation pipeline, and how it scales across servers.

Download: [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

## Table of Contents

**Getting Started**

   1. **[Getting Started With MongoDB](./[1]-Getting-Started-With-MongoDB.md)**  
       1.1 What Is MongoDB  
       1.2 Installing And mongosh  
       1.3 Databases, Collections, And Documents  
       1.4 BSON vs JSON  

**Working With Documents**

   2. **[CRUD Operations In MongoDB](./[2]-CRUD-Operations-In-MongoDB.md)**  
       2.1 Inserting Documents  
       2.2 Querying With find()  
       2.3 Updating Documents  
       2.4 Deleting Documents  
   3. **[Querying And Filtering In Depth](./[3]-Querying-And-Filtering-In-Depth.md)**  
       3.1 Query Operators  
       3.2 Sorting And Projection  
       3.3 Working With Arrays And Nested Documents  
       3.4 Indexes In MongoDB  
   4. **[The Aggregation Framework](./[4]-The-Aggregation-Framework.md)**  
       4.1 What Is An Aggregation Pipeline  
       4.2 Common Stages ($match, $group, $project)  
       4.3 Lookups (Joins In MongoDB)  
       4.4 When To Use Aggregation vs Queries  

**Scaling MongoDB**

   5. **[Schema Design In MongoDB](./[5]-Schema-Design-In-MongoDB.md)**  
       5.1 Embedding vs Referencing  
       5.2 Designing For Access Patterns  
       5.3 Schema Validation  
       5.4 Common Anti-Patterns  
   6. **[Replication, Sharding, And The Ecosystem](./[6]-Replication-Sharding-And-The-Ecosystem.md)**  
       6.1 Replica Sets  
       6.2 Sharding Basics  
       6.3 MongoDB Atlas  
       6.4 When To Choose MongoDB  