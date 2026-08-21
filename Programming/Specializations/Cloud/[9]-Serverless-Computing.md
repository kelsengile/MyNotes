[Previous](./[8]-Containers-and-Orchestration-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[10]-Auto-Scaling-and-Load-Balancing.md)

*Compute*

# Lesson 9 - Serverless Computing (Functions as a Service)

## 9.1 What Is Serverless?

**Serverless** doesn't mean there are no servers — it means you don't manage them. The cloud provider handles provisioning, scaling, patching, and capacity planning entirely; you just supply code and it runs when triggered. Serverless resources scale to zero when idle (no cost when nothing is happening) and scale out automatically under load, without any manual intervention.

---

## 9.2 Function as a Service Basics

The most common serverless compute model is **FaaS (Function as a Service)**: you write a small, single-purpose function, upload it, and configure a trigger (an HTTP request, a file upload, a queue message, a scheduled time). Examples: AWS Lambda, Azure Functions, Google Cloud Functions. A minimal AWS Lambda function in Python:

```python
def handler(event, context):
    name = event.get("name", "world")
    return {"statusCode": 200, "body": f"Hello, {name}!"}
```

You're billed only for the actual execution time and memory used, typically measured in milliseconds — there's no charge while the function isn't running.

---

## 9.3 Use Cases and Trade-offs

Serverless functions are a great fit for event-driven, short-lived, and spiky workloads: processing image uploads, responding to API requests (see Lesson 39), running scheduled jobs, or reacting to database changes. Trade-offs to be aware of:

- **Cold starts** — the first invocation after idle time can be slower as the environment initializes.
- **Execution time limits** — most FaaS platforms cap how long a single invocation can run (e.g. 15 minutes on Lambda), making them unsuitable for long-running processes.
- **Statelessness** — functions shouldn't rely on local disk or memory persisting between invocations; state belongs in a database or storage service.
- **Vendor-specific tooling** — serverless code is often tightly coupled to a specific provider's APIs, increasing lock-in.

---

[Previous](./[8]-Containers-and-Orchestration-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[10]-Auto-Scaling-and-Load-Balancing.md)
