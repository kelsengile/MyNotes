[Previous](./[10]-Auto-Scaling-and-Load-Balancing.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[12]-Block-and-File-Storage.md)

*Storage*

# Lesson 11 - Object Storage (S3-Style Buckets)

## 11.1 What Is Object Storage?

**Object storage** stores data as discrete, self-contained units called **objects**, each with data, metadata, and a unique identifier — rather than as blocks on a disk or files in a folder tree. It's designed for storing large amounts of unstructured data (images, videos, backups, logs, static website files) with virtually unlimited scalability, high durability (data is redundantly copied across multiple facilities), and access over HTTP(S) rather than a traditional filesystem. AWS S3 is the best-known example and gave the model its common nickname, "S3-style storage"; equivalents include Azure Blob Storage and Google Cloud Storage.

---

## 11.2 Buckets, Keys, and Objects

Object storage is organized simply:

- A **bucket** is a top-level container with a globally unique name (e.g. `my-company-assets`).
- Each **object** stored in a bucket has a **key** — a string that acts as its unique identifier, often resembling a file path (e.g. `images/logo.png`) even though object storage has no real folder hierarchy underneath.
- Objects can range from a few bytes to terabytes in size.

A basic AWS CLI upload looks like:

```bash
aws s3 cp ./logo.png s3://my-company-assets/images/logo.png
```

---

## 11.3 Access Control and Use Cases

Access to buckets and objects is controlled via IAM policies (Lesson 5) and bucket policies, which can restrict access to specific users/roles or, when intentional, allow public read access (common for static website assets). Common use cases include:

- Hosting static websites and frontend assets.
- Storing backups and database snapshots.
- Serving as the origin for a CDN (Lesson 18).
- Data lakes for analytics pipelines.
- Application file uploads (user avatars, documents, media).

Because object storage is billed per GB stored plus data transfer/requests, combining it with lifecycle policies (Lesson 13) helps control long-term cost.

---

[Previous](./[10]-Auto-Scaling-and-Load-Balancing.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[12]-Block-and-File-Storage.md)
