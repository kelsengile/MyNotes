[Previous](./[37]-Event-Driven-Architecture-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[39]-Building-a-Serverless-API.md)

*Serverless & Event-Driven Architecture*

# Lesson 38 - Message Queues & Pub/Sub

## 38.1 Message Queues

A **message queue** holds messages sent by a producer until a consumer is ready to process them, decoupling the sender's and receiver's timing entirely. Critically, each message in a standard queue is typically delivered to and processed by exactly **one** consumer, even if multiple consumers are listening — this makes queues a natural fit for **work distribution**, spreading a backlog of tasks across a pool of workers. AWS SQS, Azure Queue Storage, and Google Cloud Tasks are common managed queue services. Queues also naturally smooth out traffic spikes: if producers suddenly generate far more messages than consumers can immediately process, the queue simply buffers the backlog instead of overwhelming downstream systems.

---

## 38.2 Pub/Sub

**Publish/Subscribe (Pub/Sub)** is a different pattern: a producer ("publisher") sends a message to a **topic**, and *every* subscriber currently listening to that topic receives a copy of it — unlike a queue, where only one consumer gets each message. This is the natural fit for the "broadcast" style of event-driven architecture (Lesson 37), where one event (e.g. `OrderPlaced`) needs to trigger multiple independent reactions (shipping, email, analytics) simultaneously. AWS SNS, Azure Service Bus Topics, and Google Cloud Pub/Sub are common managed pub/sub services, and are often paired with queues (a topic fans out to multiple queues, one per consumer group) to get both broadcast and reliable, ordered per-consumer processing.

---

## 38.3 Common Services

| | Model | Delivery | Example Use |
|---|---|---|---|
| Queue | Point-to-point | One consumer per message | Distributing a job backlog across workers |
| Pub/Sub | Broadcast | All subscribers per message | Notifying multiple services of one event |

Managed messaging services also provide features like **dead-letter queues** (holding messages that repeatedly fail processing, so they can be investigated rather than lost or endlessly retried), **message ordering**, and **at-least-once vs exactly-once delivery guarantees** — trade-offs to consider carefully based on what your consumers can tolerate.

---

[Previous](./[37]-Event-Driven-Architecture-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[39]-Building-a-Serverless-API.md)
