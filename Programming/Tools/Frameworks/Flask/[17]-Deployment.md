[Previous](./[16]-The-Flask-Extension-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[18]-Next-Steps-With-Flask.md)

*Quality And Delivery*

# Lesson 17 - Deployment

---

## 17.1 Gunicorn And WSGI Servers

### Why not `flask run`?

The built-in server started by `flask run` is meant for **development only**. It is not designed for performance, stability, or security under real traffic. In production you run your app with a **WSGI server**, which handles many requests concurrently and manages worker processes.

### Popular WSGI servers

| Server | Notes |
|---|---|
| **Gunicorn** | The most common choice on Linux and macOS. Simple, robust, widely documented. |
| **uWSGI** | Powerful and configurable, but more complex. |
| **Waitress** | Pure Python, works on **Windows** as well as Linux. |

### Preparing an entry point

For a factory app, point the server at the factory. For a simple app, point it at the app object.

`wsgi.py`:

```python
from myapp import create_app

app = create_app()
```

### Running Gunicorn

```bash
pip install gunicorn
```

```bash
# Using a wsgi.py module with an `app` object
gunicorn --workers 3 --bind 0.0.0.0:8000 wsgi:app

# Calling the factory directly
gunicorn --workers 3 --bind 0.0.0.0:8000 "myapp:create_app()"
```

The format is `module:object` or `"module:factory()"`.

### Important options

| Option | Meaning |
|---|---|
| `--workers N` | Number of worker processes. A common starting point is `(2 x CPU cores) + 1`. |
| `--bind HOST:PORT` | Address and port to listen on. |
| `--threads N` | Threads per worker, useful for I/O-heavy apps. |
| `--timeout N` | Seconds before a silent worker is killed and restarted (default 30). |
| `--access-logfile -` | Send access logs to stdout. |
| `--error-logfile -` | Send error logs to stderr. |
| `--reload` | Auto-reload on code changes (**development only**). |

### Workers explained

Each worker is a separate process with its own memory. More workers allow more simultaneous requests, but they use more RAM. Because workers do not share memory, **do not store shared state in global variables**; use a database or Redis instead.

### A configuration file

`gunicorn.conf.py`:

```python
import multiprocessing

bind = "0.0.0.0:8000"
workers = multiprocessing.cpu_count() * 2 + 1
accesslog = "-"
errorlog = "-"
timeout = 30
```

Run with:

```bash
gunicorn -c gunicorn.conf.py wsgi:app
```

### Waitress (Windows-friendly)

```bash
pip install waitress
waitress-serve --host=0.0.0.0 --port=8000 wsgi:app
```

### Running as a service with systemd (Linux)

`/etc/systemd/system/myapp.service`:

```ini
[Unit]
Description=My Flask App
After=network.target

[Service]
User=appuser
Group=appuser
WorkingDirectory=/srv/myapp
EnvironmentFile=/srv/myapp/.env
ExecStart=/srv/myapp/.venv/bin/gunicorn --workers 3 --bind unix:/run/myapp.sock wsgi:app
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp
sudo systemctl status myapp
```

systemd starts the app on boot and restarts it if it crashes.

---

## 17.2 Nginx Reverse Proxy

### What is a reverse proxy?

A **reverse proxy** sits in front of your application. Browsers talk to **Nginx**, and Nginx forwards requests to Gunicorn.

```
Browser  →  Nginx (ports 80/443)  →  Gunicorn (port 8000 or a socket)  →  Flask
```

### Why use one?

| Benefit | Explanation |
|---|---|
| **HTTPS/TLS** | Nginx handles certificates and encryption. |
| **Static files** | Nginx serves CSS, JS, and images much faster than Python. |
| **Slow clients** | Nginx buffers slow connections so workers are not tied up. |
| **Security** | Hides the application server and can filter bad requests. |
| **Load balancing** | Distribute traffic across several app servers. |
| **Compression and caching** | Reduces bandwidth and speeds up pages. |

### A basic Nginx site configuration

`/etc/nginx/sites-available/myapp`:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    client_max_body_size 5M;

    location /static/ {
        alias /srv/myapp/myapp/static/;
        expires 30d;
        add_header Cache-Control "public";
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

If Gunicorn listens on a Unix socket (as in the systemd example), use `proxy_pass http://unix:/run/myapp.sock;`.

Enable and reload:

```bash
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
sudo nginx -t                # test the configuration
sudo systemctl reload nginx
```

### HTTPS with Let's Encrypt

**Certbot** obtains free certificates and configures Nginx for you:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

Certificates renew automatically. Certbot also sets up the redirect from HTTP to HTTPS.

### Telling Flask about the proxy

Because Nginx talks to Flask over plain HTTP, Flask would think every request is insecure and see Nginx's IP address instead of the real client's. Fix this with `ProxyFix`, and **only** use it when you really are behind a proxy you trust:

```python
from werkzeug.middleware.proxy_fix import ProxyFix

def create_app():
    app = Flask(__name__)
    app.wsgi_app = ProxyFix(app.wsgi_app, x_for=1, x_proto=1, x_host=1)
    return app
```

The numbers say how many proxies to trust for each header. With this in place:

- `request.remote_addr` is the real client IP.
- `url_for(..., _external=True)` generates `https://` URLs.
- Secure cookies work correctly.

### Useful additions

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript;

add_header X-Content-Type-Options nosniff;
add_header X-Frame-Options DENY;
```

### Firewall

Allow only ports 80 and 443 to the world. Keep Gunicorn bound to `127.0.0.1` or a Unix socket so it is not directly reachable from the internet.

---

## 17.3 Docker

**Docker** packages your app and everything it needs (Python, libraries, system packages) into a **container image** that runs the same way on any machine.

### Benefits

- Identical environments for development, testing, and production.
- Easy to deploy to many hosting platforms.
- Isolation from other apps on the same server.

### A production Dockerfile

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependencies first so Docker can cache this layer
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY . .

# Run as a non-root user
RUN useradd --create-home appuser
USER appuser

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "3", "wsgi:app"]
```

Make sure `gunicorn` is listed in `requirements.txt`.

### .dockerignore

Keep unnecessary and sensitive files out of the image:

```
.venv
__pycache__
*.pyc
.git
.env
instance
tests
```

### Building and running

```bash
docker build -t myapp .
docker run --rm -p 8000:8000 --env-file .env myapp
```

- `-p 8000:8000` maps the container's port to your machine.
- `--env-file .env` supplies environment variables (secrets are **not** baked into the image).

Visit `http://localhost:8000`.

### Docker Compose: app, database, and Nginx

`docker-compose.yml`:

```yaml
services:
  web:
    build: .
    env_file: .env
    depends_on:
      - db
    expose:
      - "8000"

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: myapp
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: myapp
    volumes:
      - pgdata:/var/lib/postgresql/data

  nginx:
    image: nginx:stable
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - web

volumes:
  pgdata:
```

```bash
docker compose up --build -d
docker compose exec web flask --app wsgi db upgrade
docker compose logs -f web
```

In `nginx.conf`, use `proxy_pass http://web:8000;`. Compose lets containers reach each other by service name.

### Container best practices

- **One process per container** (the app server).
- **Log to stdout** so Docker collects logs.
- **Never bake secrets into images**; pass them at runtime.
- Use **volumes** for data that must persist (databases, uploads).
- Keep images small with `-slim` base images.
- Pin base image versions for reproducible builds.
- Add a health-check endpoint such as `/health` returning `{"status": "ok"}`.

---

## 17.4 Platform Hosting

If you do not want to manage servers yourself, a **Platform as a Service (PaaS)** can run your app for you. You push code (or a container), and the platform handles servers, scaling, HTTPS, and often databases.

### Common options

| Platform | Style |
|---|---|
| **Render** | Deploy from Git or Docker; managed PostgreSQL available. |
| **Railway** | Git or Docker deploys with simple environment management. |
| **Fly.io** | Runs Docker containers close to users around the world. |
| **Heroku** | Classic PaaS using a `Procfile`. |
| **PythonAnywhere** | Beginner-friendly Python hosting with a web interface. |
| **Google Cloud Run / AWS App Runner / Azure App Service** | Large-cloud container and app hosting. |
| **VPS** (DigitalOcean, Linode, Hetzner, etc.) | Full control; you set up Nginx, Gunicorn, and updates yourself. |

(Pricing, free tiers, and features change often, so check each provider's current documentation.)

### What most platforms need from you

1. A `requirements.txt` listing dependencies, including `gunicorn`.
2. A **start command** that runs your WSGI server.
3. Configuration through **environment variables** (`SECRET_KEY`, `DATABASE_URL`, and so on).
4. Listening on the **port the platform provides**, usually in an environment variable named `PORT`.

### Example: Procfile

```
web: gunicorn wsgi:app
```

Gunicorn automatically reads the `PORT` variable on many platforms; if yours does not, bind explicitly:

```
web: gunicorn --bind 0.0.0.0:$PORT wsgi:app
```

### Databases on platforms

- SQLite files usually do **not** survive redeploys on these platforms because their disks are temporary. Use a **managed PostgreSQL or MySQL** database.
- Providers generally supply a connection string in `DATABASE_URL`. Some use the older prefix `postgres://`, which SQLAlchemy requires as `postgresql://`:

```python
import os

uri = os.environ.get("DATABASE_URL", "sqlite:///app.db")
if uri.startswith("postgres://"):
    uri = uri.replace("postgres://", "postgresql://", 1)
app.config["SQLALCHEMY_DATABASE_URI"] = uri
```

### Running migrations on deploy

Most platforms offer a **release command** that runs before the new version starts:

```
release: flask --app wsgi db upgrade
```

### Uploads and files

Containers and platform disks are often ephemeral. Store user uploads in **object storage** (such as S3-compatible storage) rather than on the local file system.

### Pre-launch production checklist

- [ ] `DEBUG` off, strong `SECRET_KEY` set through environment variables
- [ ] Gunicorn (or similar) instead of `flask run`
- [ ] HTTPS enabled; secure cookie flags on
- [ ] `ProxyFix` configured if behind a proxy or load balancer
- [ ] Production database with backups
- [ ] Migrations applied
- [ ] Logging to stdout; error tracking (for example Sentry)
- [ ] A health-check endpoint
- [ ] Static files served efficiently (Nginx or CDN)
- [ ] Dependencies pinned and scanned for vulnerabilities

### Zero-downtime and scaling ideas

- Run **multiple instances** behind a load balancer.
- Keep the app **stateless**: sessions in Redis or signed cookies, files in object storage.
- Use **background workers** for slow jobs (Lesson 18).
- Add **monitoring** and alerts for errors, response times, and resource usage.

---

[Previous](./[16]-The-Flask-Extension-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-Flask.md) | [Next](./[18]-Next-Steps-With-Flask.md)
