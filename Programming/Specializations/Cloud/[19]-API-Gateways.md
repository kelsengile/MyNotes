[Previous](./[18]-Content-Delivery-Networks.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[20]-Introduction-to-Infrastructure-as-Code.md)

*Networking & Content Delivery*

# Lesson 19 - API Gateways

## 19.1 What Is an API Gateway?

An **API Gateway** is a managed entry point that sits in front of your backend services (functions, containers, VMs) and handles everything incoming API requests need before reaching your actual application logic: routing, authentication, rate limiting, and request/response transformation. Instead of every backend service reimplementing these concerns individually, the gateway centralizes them. Examples include AWS API Gateway, Azure API Management, and Google Cloud API Gateway.

---

## 19.2 Common Features

- **Routing** — maps incoming request paths (e.g. `/users/{id}`) to the correct backend service or function.
- **Authentication and authorization** — validates API keys, tokens (e.g. JWT), or integrates with an identity provider before allowing a request through.
- **Rate limiting and throttling** — protects backend services from being overwhelmed by capping how many requests a client can make in a given time window.
- **Request/response transformation** — reshapes payloads between what the client sends and what the backend expects.
- **Caching** — can cache responses for frequently requested, slow-changing data, reducing backend load.
- **Monitoring and logging** — centralized visibility into API traffic, error rates, and latency.

---

## 19.3 API Gateway Patterns

A very common cloud-native pattern pairs an API Gateway with serverless functions: the gateway receives an HTTP request, authenticates it, and invokes a function (Lesson 9) to handle the business logic — with no servers to manage anywhere in the stack (explored fully in Lesson 39). In microservices architectures, an API Gateway also acts as a single, consistent public entry point in front of many internal services, hiding the internal service topology from external clients and letting internal services evolve independently as long as the gateway's contract stays stable.

---

[Previous](./[18]-Content-Delivery-Networks.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[20]-Introduction-to-Infrastructure-as-Code.md)
