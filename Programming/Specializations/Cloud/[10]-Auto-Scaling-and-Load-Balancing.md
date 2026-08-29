[Previous](./[9]-Serverless-Computing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[11]-Object-Storage.md)

*Compute*

# Lesson 10 - Auto Scaling & Load Balancing

## 10.1 What Is Auto Scaling?

**Auto scaling** automatically adjusts the number of running instances (VMs, containers) to match current demand. An **auto scaling group** is configured with a minimum, maximum, and desired instance count, plus rules for when to add or remove instances. When traffic spikes, new instances launch automatically from a template/image; when traffic drops, excess instances are terminated. This means you pay only for the capacity you actually need at any given moment, rather than provisioning for peak load year-round.

---

## 10.2 Load Balancers

A **load balancer** sits in front of a group of instances and distributes incoming traffic across them, so no single instance is overwhelmed. It also performs **health checks**, routing traffic only to instances that are responding correctly and automatically removing unhealthy ones from rotation. Common types:

- **Application Load Balancer (Layer 7)** — routes based on HTTP content (path, host header), ideal for web applications and microservices.
- **Network Load Balancer (Layer 4)** — routes based on IP/port with very high throughput and low latency, for non-HTTP traffic.

Load balancers and auto scaling groups work together: the load balancer distributes traffic, and the auto scaling group ensures there are enough healthy instances registered behind it.

---

## 10.3 Scaling Policies

Scaling can be triggered by different strategies:

- **Target tracking** — maintain a metric (e.g. average CPU at 60%) automatically by adding/removing instances.
- **Step scaling** — add or remove a specific number of instances when a threshold (e.g. CPU > 80%) is crossed.
- **Scheduled scaling** — scale up or down at predictable times (e.g. before a known daily traffic peak).
- **Predictive scaling** — uses historical patterns and machine learning to scale ahead of anticipated demand.

Choosing the right policy balances responsiveness (reacting quickly to real traffic) against stability (avoiding rapid "flapping" between scaling up and down).

---

[Previous](./[9]-Serverless-Computing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[11]-Object-Storage.md)
