[Previous](./[0]-Introduction-to-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[2]-CRUD-Operations-In-MongoDB.md)

*Getting Started*

# Lesson 1 - Getting Started With MongoDB

## 1.1 What Is MongoDB

**MongoDB** is a **document database** — a type of NoSQL database (see Database Fundamentals, Lesson 15) that stores data as flexible, JSON-like documents rather than rows in a table. Instead of designing a rigid schema up front, related data is often nested directly inside a single document, closely mirroring how objects look in application code.

> 💡 **Analogy:** If a relational database is a set of separate spreadsheets linked by ID numbers, MongoDB is more like a folder of self-contained index cards — each card holds everything about one thing, nested fields and all, without needing to flip to another card to see the full picture.

---

## 1.2 Installing And mongosh

MongoDB runs as a server process (`mongod`), typically on port `27017`. **mongosh** is the official MongoDB Shell used to connect and run commands interactively:

```bash
mongosh "mongodb://localhost:27017"
```

MongoDB can also be run as a fully managed cloud service via **MongoDB Atlas**, without installing anything locally.

---

## 1.3 Databases, Collections, And Documents

MongoDB's terminology maps to relational concepts, but isn't identical:

| MongoDB | Relational Equivalent |
|---|---|
| Database | Database |
| Collection | Table |
| Document | Row |
| Field | Column |

The key difference: documents inside the same collection **don't need identical fields**. One `users` document might have a `phone` field while another doesn't — MongoDB doesn't enforce a fixed shape by default.

```javascript
use shop
db.products.insertOne({ name: "Keyboard", price: 49.99 })
show collections
```

---

## 1.4 BSON vs JSON

MongoDB documents look like JSON but are actually stored internally as **BSON** ("Binary JSON") — a binary-encoded format that adds extra data types JSON doesn't have natively, like dates, binary data, and a special `ObjectId` type used for default document IDs. BSON is also faster to parse and more compact than text-based JSON.

[Previous](./[0]-Introduction-to-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[2]-CRUD-Operations-In-MongoDB.md)