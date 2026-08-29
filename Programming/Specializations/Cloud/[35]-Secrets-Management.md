[Previous](./[34]-Encryption-at-Rest-and-in-Transit.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[36]-Compliance-and-Shared-Responsibility-Model.md)

*Security*

# Lesson 35 - Secrets Management

## 35.1 What Are Secrets?

A **secret** is any sensitive piece of information an application needs to function but that must be kept confidential — database passwords, API keys, TLS certificates, third-party service tokens. Hardcoding secrets directly into source code or configuration files is a common but serious security risk, since anyone with access to the repository (or its history, even after deletion) can read them, and they're easy to accidentally leak by publishing a repository publicly.

---

## 35.2 Secrets Management Services

Cloud providers offer dedicated **secrets management** services designed specifically for this problem: AWS Secrets Manager, Azure Key Vault, Google Secret Manager, along with the popular third-party tool HashiCorp Vault. These services let applications retrieve secrets at runtime via an authenticated API call, rather than reading them from a config file:

```python
import boto3
client = boto3.client("secretsmanager")
secret = client.get_secret_value(SecretId="prod/db/password")
```

Access to individual secrets is controlled through IAM (Lesson 5), so only the specific services/roles that need a given secret can retrieve it, and every access can be logged and audited.

---

## 35.3 Best Practices

- **Never commit secrets to source control** — use a `.gitignore` for local config files, and secret-scanning tools to catch accidental commits before they happen.
- **Rotate secrets regularly**, and immediately after any suspected exposure — many secrets managers support automatic rotation on a schedule.
- **Use short-lived credentials where possible** (e.g. IAM roles that generate temporary tokens) instead of long-lived static secrets.
- **Separate secrets per environment** — dev, staging, and production should each use distinct credentials, so a leak in one environment doesn't compromise the others.
- **Limit blast radius** — apply least privilege so each secret is scoped only to what actually needs it.

---

[Previous](./[34]-Encryption-at-Rest-and-in-Transit.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[36]-Compliance-and-Shared-Responsibility-Model.md)
