[⬅ Back to Database Fundamentals](../[0]-Introduction-to-Databases.md)

# Introduction to Redis

Redis is an in-memory **key-value store**, prized for being extremely fast since it keeps its entire dataset in RAM rather than reading from disk on every request. It's rarely used as an application's only database — instead, it typically sits alongside one, handling caching, session storage, real-time counters, message queues, and anything else that needs sub-millisecond speed. This Topic builds on the key-value concepts from Database Fundamentals and covers Redis's data structures, persistence options, and the patterns that make it such a common companion to relational and document databases alike.

Download: [redis.io/docs/latest/operate/oss_and_stack/install](https://redis.io/docs/latest/operate/oss_and_stack/install/)

## Table of Contents

**Getting Started**

   1. **[Getting Started With Redis](./[1]-Getting-Started-With-Redis.md)**  
       1.1 What Is Redis  
       1.2 Installing And redis-cli  
       1.3 Redis As An In-Memory Store  
       1.4 Keys And Basic Commands  

**Data Structures And Commands**

   2. **[Core Redis Data Types](./[2]-Core-Redis-Data-Types.md)**  
       2.1 Strings  
       2.2 Lists And Sets  
       2.3 Hashes  
       2.4 Sorted Sets  
   3. **[Expiration, Persistence, And Durability](./[3]-Expiration-Persistence-And-Durability.md)**  
       3.1 TTL And EXPIRE  
       3.2 RDB Snapshots  
       3.3 AOF Logging  
       3.4 Choosing A Persistence Strategy  
   4. **[Pub/Sub And Messaging Patterns](./[4]-PubSub-And-Messaging-Patterns.md)**  
       4.1 Publish/Subscribe Basics  
       4.2 Redis Streams  
       4.3 Use Cases For Messaging  
       4.4 Limitations Of Redis Pub/Sub  

**Redis In Production**

   5. **[Redis As A Cache](./[5]-Redis-As-A-Cache.md)**  
       5.1 Common Caching Patterns  
       5.2 Eviction Policies  
       5.3 Cache Invalidation Strategies  
       5.4 Avoiding Cache Stampedes  
   6. **[Transactions, Scripting, And Scaling](./[6]-Transactions-Scripting-And-Scaling.md)**  
       6.1 MULTI/EXEC Transactions  
       6.2 Lua Scripting  
       6.3 Replication And Redis Cluster  
       6.4 When To Choose Redis  