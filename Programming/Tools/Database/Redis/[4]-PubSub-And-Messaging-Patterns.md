[Previous](./[3]-Expiration-Persistence-And-Durability.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[5]-Redis-As-A-Cache.md)

*Data Structures And Commands*

# Lesson 4 - Pub/Sub And Messaging Patterns

## 4.1 Publish/Subscribe Basics

Redis includes a built-in **publish/subscribe (pub/sub)** messaging system. Clients **subscribe** to named channels, and any message another client **publishes** to that channel is instantly delivered to every subscriber:

```bash
SUBSCRIBE notifications     # in one client

PUBLISH notifications "New order received"   # in another client
```

Messages are fire-and-forget — a subscriber only receives messages published *while it's connected*; nothing is stored or replayed for clients that connect later.

---

## 4.2 Redis Streams

For cases where messages *do* need to be stored, ordered, and replayed, Redis offers **Streams** — an append-only log data structure closer to a lightweight message queue:

```bash
XADD orders * customer "Ana" total 49.99
XRANGE orders - +                    # read all entries
XREAD COUNT 2 STREAMS orders 0       # read from a specific position
```

Streams also support **consumer groups**, letting multiple workers split up processing a stream without duplicating work.

---

## 4.3 Use Cases For Messaging

- **Real-time notifications** — pushing live updates to connected users (chat messages, alerts).
- **Event-driven architecture** — services publishing events that other services react to.
- **Lightweight task queues** — using Streams to distribute jobs across worker processes.

---

## 4.4 Limitations Of Redis Pub/Sub

- Plain pub/sub messages are **not persisted** — if no one is subscribed when a message is published, it's lost forever.
- It doesn't guarantee delivery or handle acknowledgments on its own — Streams are the better choice when reliability matters.
- For very large-scale or complex messaging needs, dedicated tools like Kafka or RabbitMQ are often a better fit than Redis alone.

[Previous](./[3]-Expiration-Persistence-And-Durability.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[5]-Redis-As-A-Cache.md)