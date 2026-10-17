[Previous](./[20]-Performance-And-Caching.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[22]-Next-Steps-With-ASPNET-Core.md)

*Quality And Delivery*

# Lesson 21 - Deployment

## 21.1 Publishing apps

**Publishing** compiles your app in Release mode and gathers everything needed to run it into one folder.

```bash
dotnet publish -c Release -o ./publish
```

The `publish` folder contains your `.dll`, dependencies, `appsettings.json`, `wwwroot`, and `web.config` (Windows/IIS only). Copy it to a server and run:

```bash
cd publish
dotnet MyApp.dll
```

**Deployment models:**

| Model | Command | Requires on target | Output size |
|---|---|---|---|
| **Framework-dependent** (default) | `dotnet publish -c Release` | The matching ASP.NET Core runtime installed | Small |
| **Self-contained** | `dotnet publish -c Release -r linux-x64 --self-contained` | Nothing (runtime is bundled) | Larger |
| **Single-file** | `... -r linux-x64 --self-contained -p:PublishSingleFile=true` | Nothing | One large executable |
| **Native AOT** | `-p:PublishAot=true` (project must be AOT-compatible) | Nothing | Small, fast start; restricted features (no reflection-heavy code) |

**Runtime identifiers (RIDs):** `win-x64`, `linux-x64`, `linux-arm64`, `osx-arm64`, `linux-musl-x64` (Alpine).

**Configuration at deployment.** Don't bake environment-specific settings into the build. Supply them at runtime with environment variables:

```bash
export ASPNETCORE_ENVIRONMENT=Production
export ASPNETCORE_URLS="http://0.0.0.0:8080"
export ConnectionStrings__Default="Host=db;Database=shop;Username=app;Password=..."
dotnet MyApp.dll
```

**Pre-deployment checklist:**

- Build in **Release** (optimizations on, debug-only code off).
- Production environment set (`ASPNETCORE_ENVIRONMENT=Production`) so the developer exception page is off.
- Secrets from a secure source, not from files in the repo (Lesson 14).
- Database migrations applied (script or bundle, Lesson 12).
- HTTPS and HSTS configured (Lesson 17); forwarded headers set if behind a proxy.
- Health check endpoint available (Lesson 20).
- Logging goes somewhere persistent (Lesson 14).

---

## 21.2 Docker containers

A **container** packages your app and its runtime into a portable image that runs the same everywhere. Microsoft publishes official images:

- `mcr.microsoft.com/dotnet/sdk` (build tools; large)
- `mcr.microsoft.com/dotnet/aspnet` (ASP.NET Core runtime; small)

Use a **multi-stage build**: compile with the SDK image, copy only the published output into the small runtime image.

**`Dockerfile`:**

```dockerfile
# ---- Stage 1: build ----
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

# Copy the project file first so package restore is cached when only code changes
COPY MyApp.csproj ./
RUN dotnet restore

COPY . ./
RUN dotnet publish MyApp.csproj -c Release -o /app/publish --no-restore

# ---- Stage 2: run ----
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app
COPY --from=build /app/publish ./

EXPOSE 8080
USER $APP_UID                       # run as a non-root user (provided by the .NET images)
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

Match the image tag (`10.0`) to your project's target framework (`net10.0`, `net8.0` → `8.0`, ...).

**`.dockerignore`** (keeps the build context small):

```
bin/
obj/
.git/
.vs/
*.md
Dockerfile
```

**Build and run:**

```bash
docker build -t myapp:1.0 .
docker run -d --name myapp -p 8080:8080 \
  -e ASPNETCORE_ENVIRONMENT=Production \
  -e "ConnectionStrings__Default=Host=host.docker.internal;Database=shop;Username=app;Password=secret" \
  myapp:1.0
```

Since .NET 8, containers listen on **port 8080** by default (not 80), which allows running as non-root. Map it with `-p hostPort:8080`.

**Docker Compose** runs your app plus its dependencies:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:8080"
    environment:
      ASPNETCORE_ENVIRONMENT: Production
      ConnectionStrings__Default: "Host=db;Database=shop;Username=app;Password=secret"
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: shop
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d shop"]
      interval: 5s
      timeout: 3s
      retries: 10

volumes:
  dbdata:
```

```bash
docker compose up --build
```

(Use a secrets mechanism instead of plain passwords outside local development.)

**Container tips:** containers are **ephemeral**, so keep state in external databases/volumes; log to the console (stdout) and let the platform collect it; persist **Data Protection keys** outside the container (Lesson 17); define health checks (Lesson 20).

---

## 21.3 Azure, IIS, and Linux hosting

### Azure

| Service | Good for |
|---|---|
| **Azure App Service** | Easiest PaaS for web apps/APIs; no server management |
| **Azure Container Apps** | Containers with scaling, without managing Kubernetes |
| **Azure Kubernetes Service (AKS)** | Large container orchestration |
| **Azure Functions** | Event-driven/serverless |

Quick deploy with the Azure CLI:

```bash
az login
az webapp up --name my-unique-app --runtime "DOTNETCORE:10.0" --sku B1 --location westeurope
```

Set configuration (these override `appsettings.json`) with **Application settings** and **Connection strings** in the portal or CLI:

```bash
az webapp config appsettings set --name my-unique-app --resource-group myRg \
   --settings ASPNETCORE_ENVIRONMENT=Production "Smtp__Host=smtp.example.com"
```

Use **managed identity** with Azure Key Vault and Azure SQL to avoid storing credentials.

### IIS (Windows Server)

1. Install the **.NET Hosting Bundle** (it contains the runtime and the **ASP.NET Core Module**, which connects IIS to your app).
2. Publish the app and copy it to a folder (e.g. `C:\inetpub\myapp`).
3. In IIS Manager create a **Site** pointing to the folder; set the Application Pool's **.NET CLR version** to **No Managed Code**.
4. Ensure the app pool identity can read the folder (and write to log/data folders if needed).

The generated `web.config` tells IIS to use the ASP.NET Core Module:

```xml
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" arguments=".\MyApp.dll" stdoutLogEnabled="false"
                hostingModel="inprocess" />
  </system.webServer>
</configuration>
```

`hostingModel="inprocess"` runs the app inside IIS's worker process (fastest); `outofprocess` runs Kestrel separately.

### Linux (systemd + nginx)

Run the app as a **service** so it starts on boot and restarts on failure. `/etc/systemd/system/myapp.service`:

```ini
[Unit]
Description=My ASP.NET Core App
After=network.target

[Service]
WorkingDirectory=/var/www/myapp
ExecStart=/usr/bin/dotnet /var/www/myapp/MyApp.dll
Restart=always
RestartSec=10
SyslogIdentifier=myapp
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=ASPNETCORE_URLS=http://localhost:5000

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable myapp
sudo systemctl start myapp
sudo systemctl status myapp
journalctl -u myapp -f                      # follow logs
```

**nginx** acts as the reverse proxy in front of Kestrel (`/etc/nginx/sites-available/myapp`):

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Use **Certbot / Let's Encrypt** to add HTTPS.

**Forwarded headers.** Behind a proxy or load balancer, the app sees the proxy's IP and `http` scheme. Tell ASP.NET Core to trust the `X-Forwarded-*` headers so redirects, link generation, and client IPs are correct:

```csharp
builder.Services.Configure<ForwardedHeadersOptions>(o =>
{
    o.ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    // In production, restrict to known proxies via o.KnownProxies / o.KnownNetworks
});

app.UseForwardedHeaders();      // first in the pipeline
```

(`ForwardedHeaders` is in `Microsoft.AspNetCore.HttpOverrides`.)

---

## 21.4 CI/CD basics

**Continuous Integration (CI)** automatically builds and tests every change. **Continuous Delivery/Deployment (CD)** automatically ships tested builds to an environment. Together they catch problems early and make releases repeatable.

**A typical pipeline:**

```
Push / Pull request
   → Restore → Build → Test → (Publish / Build image) → Push artifact/image → Deploy → Smoke test
```

**GitHub Actions example** (`.github/workflows/ci.yml`):

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'

      - name: Restore
        run: dotnet restore

      - name: Build
        run: dotnet build --no-restore -c Release

      - name: Test
        run: dotnet test --no-build -c Release --verbosity normal

      - name: Publish
        run: dotnet publish MyApp/MyApp.csproj -c Release -o publish

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: app
          path: publish

  docker:
    needs: build-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ghcr.io/${{ github.repository }}:latest
```

Other popular systems: **Azure DevOps Pipelines**, **GitLab CI**, **Jenkins**, **CircleCI**. The steps are the same; only the syntax differs.

**Good CD practices:**

| Practice | Why |
|---|---|
| **Same artifact everywhere** | Build once; promote the same image/package from staging to production |
| **Secrets in the CI secret store** | Never in the repository or logs |
| **Run migrations as a pipeline step** | Using a script or bundle (Lesson 12), not at app startup with many instances |
| **Environments with approvals** | Manual approval before production |
| **Smoke test after deploy** | Call `/health/ready` and a key endpoint |
| **Deployment strategies** | Rolling updates, blue/green, or canary to reduce downtime and risk |
| **Rollback plan** | Keep previous versions available; make migrations backward-compatible |

**Zero-downtime tip:** make database changes **additive** first (add a column), deploy code that works with both schemas, then remove old columns in a later release.

---

[Previous](./[20]-Performance-And-Caching.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[22]-Next-Steps-With-ASPNET-Core.md)
