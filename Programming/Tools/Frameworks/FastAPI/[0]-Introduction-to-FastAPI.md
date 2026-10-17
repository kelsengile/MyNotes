[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to Python](/Programming/Syntax/Python/[0]-Introduction-to-Python.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/fastapi/fastapi-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">FastAPI</h1>
</div>


FastAPI is a modern, high-performance Python framework for building APIs. It uses standard Python type hints to automatically validate requests, serialize responses, and generate interactive documentation, so you write less code and catch more errors.

Built on Starlette and Pydantic, FastAPI supports asynchronous code out of the box and runs on ASGI servers. This Topic covers parameters, request bodies, dependency injection, databases, authentication, background tasks, WebSockets, testing, and deployment.

### Why Study FastAPI?

- **Type hints do the heavy lifting** — validation, parsing, and documentation come from the code you already write.
- **Automatic interactive docs** — Swagger UI and ReDoc are generated for every API.
- **Fast in practice** — async support and ASGI make it one of the quickest Python frameworks.
- **Ideal for machine learning services** — a popular way to serve models and data pipelines as APIs.

### FastAPI At A Glance

| Attribute | Details |
|---|---|
| Language | Python |
| Type | API framework |
| Built on | Starlette and Pydantic |
| Best for | REST APIs, microservices, ML model serving |
| Pairs well with | Uvicorn, SQLModel, SQLAlchemy, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is FastAPI](./[1]-What-Is-FastAPI.md)**  
       1.1 Why FastAPI exists  
       1.2 ASGI, Starlette, and Pydantic  
       1.3 Installing and running with Uvicorn  
       1.4 Your first API and its docs  
   2. **[Python Type Hints And Async](./[2]-Python-Type-Hints-And-Async.md)**  
       2.1 Type hints refresher  
       2.2 async def vs def  
       2.3 Concurrency in practice  
       2.4 When async helps  
   3. **[Path And Query Parameters](./[3]-Path-And-Query-Parameters.md)**  
       3.1 Path parameters  
       3.2 Query parameters  
       3.3 Validation constraints  
       3.4 Enums and defaults  
   4. **[Request Bodies With Pydantic](./[4]-Request-Bodies-With-Pydantic.md)**  
       4.1 Defining models  
       4.2 Nested models  
       4.3 Field validation  
       4.4 Examples in docs  
   5. **[Responses And Status Codes](./[5]-Responses-And-Status-Codes.md)**  
       5.1 response_model  
       5.2 Status codes  
       5.3 Response classes  
       5.4 Headers and cookies  

**Core Features**

   6. **[Dependency Injection](./[6]-Dependency-Injection.md)**  
       6.1 What dependencies are  
       6.2 Sub-dependencies  
       6.3 Dependencies with yield  
       6.4 Global and router-level dependencies  
   7. **[Validation And Error Handling](./[7]-Validation-And-Error-Handling.md)**  
       7.1 Automatic validation errors  
       7.2 HTTPException  
       7.3 Custom exception handlers  
       7.4 Customizing error responses  
   8. **[Forms, Files, And Headers](./[8]-Forms-Files-And-Headers.md)**  
       8.1 Form data  
       8.2 File uploads  
       8.3 Headers and cookies  
       8.4 Streaming responses  
   9. **[Routers And Project Structure](./[9]-Routers-And-Project-Structure.md)**  
       9.1 APIRouter  
       9.2 Splitting a large app  
       9.3 Prefixes and tags  
       9.4 Suggested layouts  
   10. **[Middleware And Background Tasks](./[10]-Middleware-And-Background-Tasks.md)**  
       10.1 Writing middleware  
       10.2 CORS  
       10.3 Background tasks  
       10.4 Lifespan events  
   11. **[WebSockets](./[11]-WebSockets.md)**  
       11.1 Connecting clients  
       11.2 Sending and receiving messages  
       11.3 Managing connections  
       11.4 Use cases  

**Data**

   12. **[SQL Databases With SQLModel And SQLAlchemy](./[12]-SQL-Databases-With-SQLModel-And-SQLAlchemy.md)**  
       12.1 Models and sessions  
       12.2 CRUD operations  
       12.3 Relationships  
       12.4 Dependency-injected sessions  
   13. **[Migrations And Async Databases](./[13]-Migrations-And-Async-Databases.md)**  
       13.1 Alembic migrations  
       13.2 Async database drivers  
       13.3 Connection pooling  
       13.4 NoSQL options  

**Security**

   14. **[Authentication With OAuth2 And JWT](./[14]-Authentication-With-OAuth2-And-JWT.md)**  
       14.1 Security utilities  
       14.2 Password hashing  
       14.3 JWT tokens  
       14.4 Protecting endpoints  
   15. **[CORS And API Security](./[15]-CORS-And-API-Security.md)**  
       15.1 CORS configuration  
       15.2 Scopes and permissions  
       15.3 HTTPS and headers  
       15.4 Rate limiting  

**Quality And Delivery**

   16. **[Testing FastAPI Applications](./[16]-Testing-FastAPI-Applications.md)**  
       16.1 TestClient  
       16.2 Pytest fixtures  
       16.3 Overriding dependencies  
       16.4 Async tests  
   17. **[OpenAPI And Documentation](./[17]-OpenAPI-And-Documentation.md)**  
       17.1 Swagger UI and ReDoc  
       17.2 Customizing metadata  
       17.3 Tags and examples  
       17.4 Generating client code  
   18. **[Deployment](./[18]-Deployment.md)**  
       18.1 Uvicorn and Gunicorn  
       18.2 Docker  
       18.3 Environment configuration  
       18.4 Cloud hosting  
   19. **[Next Steps With FastAPI](./[19]-Next-Steps-With-FastAPI.md)**  
       19.1 Performance tuning  
       19.2 Task queues  
       19.3 Serving ML models  
       19.4 Project ideas
