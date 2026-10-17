[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to Python](/Programming/Syntax/Python/[0]-Introduction-to-Python.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/flask/flask-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Flask</h1>
</div>


Flask is a lightweight Python micro-framework for building web applications and APIs. It provides routing, request handling, and templating, and deliberately leaves everything else, such as databases, forms, and authentication, to extensions you add only when you need them.

That minimal core makes Flask easy to learn and very flexible. This Topic covers routing, templates, forms, the application factory pattern, databases, building JSON APIs, authentication, testing, and deployment.

### Why Study Flask?

- **A great first Python web framework** — a working app can fit in a handful of lines.
- **You choose your tools** — pick only the extensions and libraries you want.
- **Teaches how the web works** — with less hidden magic, you see requests, responses, and sessions clearly.
- **Ideal for small services and prototypes** — popular for APIs, microservices, and internal tools.

### Flask At A Glance

| Attribute | Details |
|---|---|
| Language | Python |
| Type | Micro-framework |
| Built on | Werkzeug and Jinja2 |
| Best for | Small apps, prototypes, microservices, simple APIs |
| Pairs well with | SQLAlchemy, Flask-Login, Gunicorn, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is Flask](./[1]-What-Is-Flask.md)**  
       1.1 Micro-framework meaning  
       1.2 WSGI explained  
       1.3 Virtual environments and installation  
       1.4 Your first Flask app  
   2. **[Routing And Views](./[2]-Routing-And-Views.md)**  
       2.1 The route decorator  
       2.2 Variable rules  
       2.3 HTTP methods  
       2.4 URL building with url_for  
   3. **[Requests And Responses](./[3]-Requests-And-Responses.md)**  
       3.1 The request object  
       3.2 Returning responses  
       3.3 Redirects and status codes  
       3.4 Cookies and headers  
   4. **[Jinja2 Templates](./[4]-Jinja2-Templates.md)**  
       4.1 Rendering templates  
       4.2 Variables, loops, and conditions  
       4.3 Template inheritance  
       4.4 Static files  

**Building Applications**

   5. **[Forms With Flask-WTF](./[5]-Forms-With-Flask-WTF.md)**  
       5.1 Form classes  
       5.2 Validation  
       5.3 CSRF protection  
       5.4 Flash messages  
   6. **[Application Factory And Blueprints](./[6]-Application-Factory-And-Blueprints.md)**  
       6.1 Why structure matters  
       6.2 The application factory pattern  
       6.3 Blueprints  
       6.4 Project layout  
   7. **[Configuration And Environments](./[7]-Configuration-And-Environments.md)**  
       7.1 Config objects  
       7.2 Environment variables  
       7.3 Development vs production  
       7.4 Instance folders  
   8. **[Sessions And Cookies](./[8]-Sessions-And-Cookies.md)**  
       8.1 How sessions work  
       8.2 Storing session data  
       8.3 Secret keys  
       8.4 Server-side sessions  
   9. **[Error Handling And Logging](./[9]-Error-Handling-And-Logging.md)**  
       9.1 Custom error pages  
       9.2 Error handlers  
       9.3 Logging setup  
       9.4 Debug mode  

**Data**

   10. **[Databases With SQLAlchemy](./[10]-Databases-With-SQLAlchemy.md)**  
       10.1 Flask-SQLAlchemy  
       10.2 Models and relationships  
       10.3 Querying  
       10.4 Sessions and transactions  
   11. **[Migrations With Flask-Migrate](./[11]-Migrations-With-Flask-Migrate.md)**  
       11.1 Alembic basics  
       11.2 Creating migrations  
       11.3 Applying and reverting  
       11.4 Team workflows  
   12. **[Building JSON APIs](./[12]-Building-JSON-APIs.md)**  
       12.1 jsonify and request.json  
       12.2 REST endpoint design  
       12.3 Serialization with Marshmallow  
       12.4 CORS  

**Security**

   13. **[Authentication With Flask-Login](./[13]-Authentication-With-Flask-Login.md)**  
       13.1 User sessions  
       13.2 Login and logout  
       13.3 Password hashing  
       13.4 Protecting routes  
   14. **[Security Basics](./[14]-Security-Basics.md)**  
       14.1 Common vulnerabilities  
       14.2 Secure cookies  
       14.3 Input validation  
       14.4 Secrets handling  

**Quality And Delivery**

   15. **[Testing With Pytest](./[15]-Testing-With-Pytest.md)**  
       15.1 Test client  
       15.2 Fixtures  
       15.3 Testing databases  
       15.4 Coverage  
   16. **[The Flask Extension Ecosystem](./[16]-The-Flask-Extension-Ecosystem.md)**  
       16.1 Finding extensions  
       16.2 Popular extensions  
       16.3 Writing a simple extension  
       16.4 Choosing wisely  
   17. **[Deployment](./[17]-Deployment.md)**  
       17.1 Gunicorn and WSGI servers  
       17.2 Nginx reverse proxy  
       17.3 Docker  
       17.4 Platform hosting  
   18. **[Next Steps With Flask](./[18]-Next-Steps-With-Flask.md)**  
       18.1 Async in Flask  
       18.2 Celery background tasks  
       18.3 Moving to FastAPI or Django  
       18.4 Project ideas
