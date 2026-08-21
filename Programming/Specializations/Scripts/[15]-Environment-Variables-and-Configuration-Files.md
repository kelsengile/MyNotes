[Previous](./[14]-Scheduling-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[16]-Error-Handling-and-Exit-Codes.md)

*Automation Basics*

# Lesson 15 - Environment Variables & Configuration Files

## 15.1 What Are Environment Variables?

Environment variables are key-value pairs available to a process and its children. They're commonly used for configuration that shouldn't be hardcoded, such as API keys or file paths.

```bash
export API_KEY="abc123"
echo "$API_KEY"

# In Python:
import os
api_key = os.environ.get("API_KEY")
```

---

## 15.2 .env Files

Rather than exporting variables manually every session, many projects use a `.env` file:

```
API_KEY=abc123
DEBUG=false
```

Bash can load it with:

```bash
set -a
source .env
set +a
```

Python projects commonly use the `python-dotenv` package to load `.env` files automatically.

**Never commit `.env` files containing secrets to version control** — add them to `.gitignore`.

---

## 15.3 Configuration Files

For more structured configuration, scripts often read from `.ini`, `.yaml`, `.json`, or `.toml` files instead of (or alongside) environment variables. Environment variables are best for secrets and per-environment overrides; config files are best for larger, structured settings that are checked into version control.

---

[Previous](./[14]-Scheduling-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[16]-Error-Handling-and-Exit-Codes.md)
