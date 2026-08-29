[Previous](./[36]-Compliance-and-Shared-Responsibility-Model.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[38]-Message-Queues-and-Pub-Sub.md)

*Serverless & Event-Driven Architecture*

# Lesson 37 - Event-Driven Architecture Basics

## 37.1 What Is Event-Driven Architecture?

**Event-driven architecture (EDA)** structures a system around **events** — records of something that happened (a user signed up, an order was placed, a file was uploaded) — rather than direct, synchronous calls between services. Instead of Service A calling Service B directly and waiting for a response, Service A emits an event, and any interested services react to it independently, whenever they're ready. This is a significant shift from traditional request/response architectures, and pairs naturally with serverless computing (Lesson 9), since many FaaS triggers are themselves events.

---

## 37.2 Events, Producers, Consumers

- **Producer** — the component that generates and emits an event (e.g. an e-commerce service publishing an `OrderPlaced` event).
- **Event** — a small, immutable record describing what happened, typically including a type, timestamp, and relevant data.
- **Consumer** — any component that subscribes to and reacts to events (e.g. a shipping service that reacts to `OrderPlaced` by scheduling a shipment, an email service that sends a confirmation).

Producers don't need to know who (or how many services) will consume their events — this loose coupling is the defining trait of event-driven systems.

---

## 37.3 Benefits and Challenges

**Benefits:**
- **Loose coupling** — producers and consumers can be developed, deployed, and scaled independently.
- **Scalability** — consumers can be added or scaled without changing the producer at all.
- **Resilience** — if one consumer is temporarily down, events can queue up and be processed once it recovers, rather than the whole system failing.

**Challenges:**
- **Eventual consistency** — since processing happens asynchronously, different parts of the system may briefly be out of sync.
- **Debugging complexity** — tracing a chain of asynchronous events across many services is harder than following a single synchronous call stack, making tools like distributed tracing (Lesson 31) especially important.
- **Event ordering and duplication** — systems must be designed to handle events arriving out of order or more than once.

---

[Previous](./[36]-Compliance-and-Shared-Responsibility-Model.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[38]-Message-Queues-and-Pub-Sub.md)
