[Previous](./[30]-Logging-and-Metrics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[32]-Alerting-and-Incident-Response.md)

*Monitoring & Observability*

# Lesson 31 - Distributed Tracing

## 31.1 What Is Distributed Tracing?

In a microservices architecture, a single user request might pass through many services — an API gateway, an authentication service, a database, a payment service — before a response is returned. When that request is slow or fails, logs and metrics from any one service alone often can't explain *why*, since the problem might originate in a different service than where it was noticed. **Distributed tracing** follows a single request as it travels across every service it touches, reconstructing the full end-to-end journey.

---

## 31.2 Traces, Spans, Context Propagation

A **trace** represents the complete journey of one request through a system. It's composed of multiple **spans**, each representing one unit of work (e.g. "call the database," "call the payment service"), with timing information and metadata. Spans are linked in a parent-child hierarchy showing which calls triggered which. This works via **context propagation**: a unique trace ID is generated at the start of a request and passed along (usually in HTTP headers) to every downstream service call, letting each service's spans be tied back to the same overall trace even though they run in completely separate processes/machines.

---

## 31.3 Tracing Tools

Common distributed tracing tools and standards:

- **OpenTelemetry** — a vendor-neutral, open standard for generating and exporting traces (as well as metrics and logs), now widely adopted as the common instrumentation layer applications use.
- **AWS X-Ray**, **Azure Application Insights**, **Google Cloud Trace** — provider-native tracing services.
- **Jaeger**, **Zipkin** — open-source tracing backends for storing and visualizing traces.

A trace visualization typically shows a "waterfall" view of spans, immediately making it clear which specific downstream call was the slow or failing part of a request — something that's very hard to see from logs or metrics alone.

---

[Previous](./[30]-Logging-and-Metrics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[32]-Alerting-and-Incident-Response.md)
