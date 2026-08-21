[Previous](./[38]-Message-Queues-and-Pub-Sub.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[40]-Cloud-Billing-and-Pricing-Models.md)

*Serverless & Event-Driven Architecture*

# Lesson 39 - Building a Serverless API

## 39.1 Architecture Overview

A fully serverless API combines several services covered earlier in this course into one cohesive, no-servers-to-manage stack: an **API Gateway** (Lesson 19) as the entry point, **FaaS functions** (Lesson 9) for business logic, and a **managed database** (Lessons 14–15) for persistence. Requests flow: client → API Gateway → function → database → response back through the same path. Because every layer scales automatically and independently, this pattern handles traffic ranging from zero to very high volume without any manual capacity planning.

---

## 39.2 API Gateway + Functions + Database

A typical request path for `GET /users/{id}`:

1. The **API Gateway** receives the HTTP request, validates an auth token, and routes it to a specific function based on the path/method.
2. The **function** runs, extracts `id` from the request, and queries the database.
3. The **database** (e.g. DynamoDB or a serverless-friendly relational option) returns the requested data.
4. The function formats a response, which the API Gateway returns to the client.

Example minimal handler:

```python
import boto3
table = boto3.resource("dynamodb").Table("Users")

def handler(event, context):
    user_id = event["pathParameters"]["id"]
    item = table.get_item(Key={"id": user_id}).get("Item")
    if not item:
        return {"statusCode": 404, "body": "Not found"}
    return {"statusCode": 200, "body": str(item)}
```

---

## 39.3 Deployment Considerations

Building a real serverless API involves more than the happy path:

- **IaC deployment** — define the whole stack (gateway routes, functions, database, IAM roles) in Terraform or CloudFormation (Lessons 20–22) rather than clicking through consoles, so it's reproducible and version-controlled.
- **Least-privilege IAM per function** — each function should have a role granting only the exact permissions it needs (e.g. read access to one specific table), not broad account access.
- **Cold start awareness** — for latency-sensitive endpoints, consider provisioned concurrency or keeping functions "warm."
- **Observability** — centralized logging, metrics, and tracing (Lessons 30–31) are essential since there's no server to log into directly.
- **CI/CD** — automate testing and deployment of both function code and infrastructure changes together (Lessons 27–29).

---

[Previous](./[38]-Message-Queues-and-Pub-Sub.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[40]-Cloud-Billing-and-Pricing-Models.md)
