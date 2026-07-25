# [12] Private & Internal Registries

## Why Use a Private Registry?

Public registries are great for open-source, reusable code — but organizations often have code they want to share **internally**, across teams or projects, without publishing it to the public internet. That's what a **private registry** is for.

Common reasons to use one:
- Proprietary/internal libraries that shouldn't be public
- Company-specific tooling shared across teams
- Regulatory or security requirements around code distribution
- Faster, more reliable installs via a local network instead of the public internet

## Common Private Registry Solutions

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

## Scoped Packages (npm)

npm supports **scoped** package names (e.g. `@my-org/internal-utils`), which are commonly used to namespace private/internal packages and distinguish them from public ones.

```bash
npm install @my-org/internal-utils
```

## Pointing Your Package Manager at a Private Registry

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

## Registry Proxies / Pull-Through Caches

Many private registry tools can also act as a **proxy** for the public registry — meaning requests for public packages get cached locally, speeding up installs and providing a fallback if the public registry has an outage, while requests for internal packages are served directly.

## Authentication

Unlike most public registry downloads (which are open/anonymous), private registries typically require authentication — an API token, SSO login, or credentials tied to your organization's identity provider. Tokens are usually stored in a config file or environment variable, and should **never** be committed to source control.

## Try It Yourself

If your organization uses a private registry, look at your project's `.npmrc`, `pip.conf`, or equivalent config file and identify which packages are being pulled from a private source vs. the public registry.

## Up Next

Next: **package security and supply-chain risks** — what can go wrong, and how to defend against it.

---
⬅ [11] [Monorepos & Workspaces](./%5B11%5D-Monorepos-and-Workspaces.md) | ➡ [13] [Package Security & Supply-Chain Risks](./%5B13%5D-Package-Security-and-Supply-Chain-Risks.md)
