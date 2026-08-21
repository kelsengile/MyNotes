[Previous](./[29]-Domains-DNS-and-HTTPS.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[31]-Performance-Optimization.md)

*Deployment & Production*

# Lesson 30 - CI/CD for Web Projects

## 30.1 Continuous Integration (CI)

**Continuous Integration** means automatically running checks — linting (Lesson 15), tests (Lesson 16), builds (Lesson 13) — every time code is pushed or a pull request (Lesson 14) is opened, rather than relying on developers to remember to run them locally. If any check fails, the pull request is flagged before it can be merged, catching problems early while they're cheap to fix.

---

## 30.2 Continuous Deployment/Delivery (CD)

**Continuous Deployment** takes this further: once code passes all checks and merges into the main branch, it's automatically built and deployed to production without manual intervention. **Continuous Delivery** is a slightly softer version — the code is automatically prepared and ready to deploy, but a human still triggers the final release. Both remove manual, error-prone deployment steps from the process.

---

## 30.3 A Basic GitHub Actions Workflow

**GitHub Actions** is a common CI/CD tool that runs directly from a repository:

```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

This runs automatically on every push and pull request, giving immediate feedback without anyone needing to run these commands by hand.

---

## 30.4 Deploying from CI

A deploy step can be added once tests pass, often only on the main branch:

```yaml
  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh
```

The `needs: test` ensures deployment never runs if the test job failed, and the `if` condition restricts deployment to the main branch only, keeping feature-branch pushes from touching production.

---

## 30.5 Secrets in CI

CI pipelines often need credentials (deployment tokens, API keys) but these should never be hardcoded into the workflow file — CI platforms provide encrypted **secrets** storage, referenced in workflows (e.g. `${{ secrets.DEPLOY_TOKEN }}`) but never exposed in logs or the repository itself, following the same principle covered for environment variables in Lesson 27.

---

[Previous](./[29]-Domains-DNS-and-HTTPS.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[31]-Performance-Optimization.md)
