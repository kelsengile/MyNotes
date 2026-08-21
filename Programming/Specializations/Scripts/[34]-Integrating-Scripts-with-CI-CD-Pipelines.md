[Previous](./[33]-Packaging-and-Distributing-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[35]-Cross-Platform-Scripting-Considerations.md)

*Integration & Tooling*

# Lesson 34 - Integrating Scripts With CI/CD Pipelines

## 34.1 What Is CI/CD?

**Continuous Integration / Continuous Deployment** pipelines automatically run scripts (tests, builds, deployments) whenever code changes are pushed. Scripts are the building blocks that make each pipeline step work.

---

## 34.2 A Simple GitHub Actions Example

```yaml
name: Run Tests
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run script
        run: python3 run_tests.py
```

Each `run:` step is effectively a small shell script executed in the pipeline's environment.

---

## 34.3 Designing Scripts for CI/CD

- Exit with a non-zero status on failure (Lesson 16) — CI systems use the exit code to decide pass/fail.
- Avoid interactive prompts; CI runs unattended.
- Read secrets from environment variables/CI secret stores, never hardcode them.
- Keep pipeline scripts idempotent so re-running a failed job is safe.

---

## 34.4 Common Pipeline Script Types

| Stage | Typical script tasks |
|---|---|
| Build | compile, install dependencies |
| Test | run unit/integration tests, lint |
| Deploy | package artifacts, push to a server or registry |

---

[Previous](./[33]-Packaging-and-Distributing-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[35]-Cross-Platform-Scripting-Considerations.md)
