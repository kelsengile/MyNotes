[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to Python](/Programming/Syntax/Python/[0]-Introduction-to-Python.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/django/django-plain.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Django</h1>
</div>


Django is a "batteries-included" Python web framework designed for rapid development of secure, maintainable websites. It ships with an ORM, an automatic admin interface, authentication, form handling, and templating, so most common web features are available from day one.

Django follows a Model-Template-View pattern and strongly encourages clean, reusable project design. This Topic covers projects and apps, URLs and views, templates, models and the ORM, the admin, forms, authentication, Django REST Framework, security, testing, and deployment.

Download: [djangoproject.com/download](https://www.djangoproject.com/download/)

### Why Study Django?

- **Build complete apps quickly** — admin, auth, and ORM come ready to use.
- **Security is built in** — protections against CSRF, XSS, and SQL injection are enabled by default.
- **Scales to serious sites** — used by large products with heavy traffic and complex data.
- **Great Python career path** — Django is one of the most requested Python web skills.

### Django At A Glance

| Attribute | Details |
|---|---|
| Language | Python |
| Type | Full-stack backend framework |
| Pattern | MTV (Model-Template-View) |
| Best for | Content sites, dashboards, data-driven apps |
| Pairs well with | PostgreSQL, Django REST Framework, Celery, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is Django](./[1]-What-Is-Django.md)**  
       1.1 The batteries-included philosophy  
       1.2 Django's MTV architecture  
       1.3 Virtual environments and installation  
       1.4 Creating your first project  
   2. **[Projects And Apps](./[2]-Projects-And-Apps.md)**  
       2.1 Project vs app  
       2.2 Creating and registering apps  
       2.3 Settings overview  
       2.4 Running the development server  
   3. **[URLs And Views](./[3]-URLs-And-Views.md)**  
       3.1 URL routing  
       3.2 Function-based views  
       3.3 Request and response objects  
       3.4 Shortcuts and error pages  
   4. **[Templates](./[4]-Templates.md)**  
       4.1 Django Template Language  
       4.2 Template inheritance  
       4.3 Filters and tags  
       4.4 Static files  

**Data**

   5. **[Models And The ORM](./[5]-Models-And-The-ORM.md)**  
       5.1 Defining models  
       5.2 Field types and options  
       5.3 Creating and saving objects  
       5.4 Model methods and Meta  
   6. **[Migrations](./[6]-Migrations.md)**  
       6.1 How migrations work  
       6.2 Creating and applying migrations  
       6.3 Reversing and squashing  
       6.4 Data migrations  
   7. **[Querying With QuerySets](./[7]-Querying-With-QuerySets.md)**  
       7.1 Filtering and ordering  
       7.2 Lookups and Q objects  
       7.3 Aggregation and annotation  
       7.4 Relations and select_related  
   8. **[Relationships](./[8]-Relationships.md)**  
       8.1 One-to-many  
       8.2 Many-to-many  
       8.3 One-to-one  
       8.4 Related names and reverse lookups  
   9. **[The Django Admin](./[9]-The-Django-Admin.md)**  
       9.1 Registering models  
       9.2 Customizing ModelAdmin  
       9.3 Filters and search  
       9.4 Admin security  

**Building Features**

   10. **[Forms And Validation](./[10]-Forms-And-Validation.md)**  
       10.1 Form classes  
       10.2 ModelForms  
       10.3 Validation and error display  
       10.4 File uploads  
   11. **[Class-Based Views](./[11]-Class-Based-Views.md)**  
       11.1 Generic views  
       11.2 ListView and DetailView  
       11.3 Create, update, and delete views  
       11.4 Mixins  
   12. **[Authentication And Permissions](./[12]-Authentication-And-Permissions.md)**  
       12.1 User model and login  
       12.2 Custom user models  
       12.3 Permissions and groups  
       12.4 Password management  
   13. **[Middleware, Sessions, And Caching](./[13]-Middleware-Sessions-And-Caching.md)**  
       13.1 Middleware  
       13.2 Sessions and cookies  
       13.3 Cache framework  
       13.4 Signals  
   14. **[Static And Media Files](./[14]-Static-And-Media-Files.md)**  
       14.1 Static files in development  
       14.2 Media uploads  
       14.3 collectstatic  
       14.4 Storage backends  

**APIs**

   15. **[Django REST Framework](./[15]-Django-REST-Framework.md)**  
       15.1 Serializers  
       15.2 API views and viewsets  
       15.3 Routers  
       15.4 Authentication and permissions  

**Quality And Delivery**

   16. **[Testing Django Applications](./[16]-Testing-Django-Applications.md)**  
       16.1 Test client  
       16.2 Unit and integration tests  
       16.3 Fixtures and factories  
       16.4 Coverage  
   17. **[Security Best Practices](./[17]-Security-Best-Practices.md)**  
       17.1 Built-in protections  
       17.2 Settings hardening  
       17.3 Secrets management  
       17.4 Security checks  
   18. **[Settings And Environments](./[18]-Settings-And-Environments.md)**  
       18.1 Splitting settings  
       18.2 Environment variables  
       18.3 Debug mode and logging  
       18.4 Twelve-factor configuration  
   19. **[Deployment](./[19]-Deployment.md)**  
       19.1 WSGI and ASGI servers  
       19.2 Static files in production  
       19.3 Databases and Docker  
       19.4 CI/CD basics  
   20. **[Next Steps With Django](./[20]-Next-Steps-With-Django.md)**  
       20.1 Celery and background tasks  
       20.2 Async Django  
       20.3 HTMX with Django  
       20.4 Project ideas
