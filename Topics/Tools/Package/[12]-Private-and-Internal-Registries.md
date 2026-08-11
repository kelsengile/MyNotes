[Previous](./[11]-Monorepos-And-Workspaces.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Package-Security-And-Supply-Chain-Risks.md)

---

# Lesson 12 - Private And Internal Registries

## 12.1 Why Use a Private Registry?

Public registries are great for open-source, reusable code — but organizations often have code they want to share **internally**, across teams or projects, without publishing it to the public internet. That's what a **private registry** is for.

Common reasons to use one:
- Proprietary/internal libraries that shouldn't be public
- Company-specific tooling shared across teams
- Regulatory or security requirements around code distribution
- Faster, more reliable installs via a local network instead of the public internet

---

## 12.2 Common Private Registry Solutions

| Tool | Ecosystem(s) |
|---|---|
| Verdaccio | npm-compatible |
| GitHub Packages | npm, Maven, NuGet, Docker, and more |
| GitLab Package Registry | npm, PyPI, Maven, and more |
| Artifactory (JFrog) | Multi-ecosystem |
| Nexus Repository | Multi-ecosystem |
| Azure Artifacts | npm, NuGet, Maven, PyPI |
| AWS CodeArtifact | npm, PyPI, Maven, NuGet |
| Google Artifact Registry | Multi-ecosystem |

---

## 12.3 Scoped Packages (npm)

npm supports **scoped** package names (e.g. `@my-org/internal-utils`), which are commonly used to namespace private/internal packages and distinguish them from public ones.

```bash
npm install @my-org/internal-utils
```

---

## 12.4 Pointing Your Package Manager at a Private Registry

Most package managers let you configure a custom registry URL, either globally, per-project, or scoped to a specific package namespace.

**npm** (`.npmrc`):
```
@my-org:registry=https://npm.mycompany.com
//npm.mycompany.com/:_authToken=${NPM_TOKEN}
```

**pip** (`pip.conf` or command-line flag):
```bash
pip install --index-url https://pypi.mycompany.com/simple/ internal-tool
```

**Cargo** (`.cargo/config.toml`):
```toml
[registries]
my-company = { index = "https://cargo.mycompany.com/index" }
```

---

## 12.5 Registry Proxies / Pull-Through Caches

Many private registry tools can also act as a **proxy** for the public registry — meaning requests for public packages get cached locally, speeding up installs and providing a fallback if the public registry has an outage, while requests for internal packages are served directly.

---

## 12.6 Authentication

Unlike most public registry downloads (which are open/anonymous), private registries typically require authentication — an API token, SSO login, or credentials tied to your organization's identity provider. Tokens are usually stored in a config file or environment variable, and should **never** be committed to source control.

---

[Previous](./[11]-Monorepos-And-Workspaces.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Package-Security-And-Supply-Chain-Risks.md)
