[Previous](./[29]-Blue-Green-and-Canary-Deployments.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[31]-Distributed-Tracing.md)

*Monitoring & Observability*

# Lesson 30 - Logging & Metrics

## 30.1 Why Logging and Metrics Matter

Once an application is running in the cloud, across many instances/containers that scale up and down, you can't just SSH into "the server" to see what's happening — there may be dozens of ephemeral instances at any moment. **Observability** — logging, metrics, and tracing (Lesson 31) together — gives you visibility into system behavior without needing direct access to every machine. Logging and metrics are the two most fundamental pillars: logs tell you *what happened*, metrics tell you *how much/how often*.

---

## 30.2 Centralized Logging

Each application instance produces logs, but with many ephemeral instances, logs stored only locally on each machine disappear when the machine terminates and are impossible to search across the whole fleet. **Centralized logging** ships logs from every instance to a single, durable, searchable location. Common services: AWS CloudWatch Logs, Azure Monitor Logs, Google Cloud Logging, and third-party tools like the ELK stack (Elasticsearch, Logstash, Kibana) or Datadog. A good logging practice is **structured logging** — emitting logs as JSON with consistent fields (timestamp, severity, request ID) rather than free-form text, making them far easier to search and filter.

---

## 30.3 Metrics and Dashboards

**Metrics** are numeric measurements collected over time — CPU utilization, request count, error rate, response latency — typically aggregated and visualized as time-series graphs on a **dashboard**. Unlike logs (which record discrete events), metrics are efficient to store and query even at very high volume, since they're pre-aggregated numbers rather than full event text. Common metric services: AWS CloudWatch Metrics, Azure Monitor Metrics, Google Cloud Monitoring, or open-source tools like Prometheus with Grafana for visualization. A useful standard set to track for any service is the "four golden signals": **latency**, **traffic**, **errors**, and **saturation**. Metrics also feed directly into auto scaling (Lesson 10) and alerting (Lesson 32).

---

[Previous](./[29]-Blue-Green-and-Canary-Deployments.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[31]-Distributed-Tracing.md)
