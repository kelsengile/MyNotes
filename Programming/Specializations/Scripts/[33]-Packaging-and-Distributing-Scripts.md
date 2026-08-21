[Previous](./[32]-Version-Control-for-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[34]-Integrating-Scripts-with-CI-CD-Pipelines.md)

*Integration & Tooling*

# Lesson 33 - Packaging & Distributing Scripts

## 33.1 Making Bash Scripts Distributable

- Add a clear shebang (`#!/bin/bash` or `#!/usr/bin/env bash`)
- Document required arguments and dependencies in a header comment or README
- Avoid hardcoded paths specific to one machine
- Consider placing shared scripts in `/usr/local/bin` so they're on `$PATH`

---

## 33.2 Packaging Python Scripts

For a single-file script, declaring dependencies in a header comment or `requirements.txt` is often enough:

```
requests>=2.31
```

```bash
pip install -r requirements.txt
```

For a reusable tool meant to be installed via `pip`, structure it as a proper package with a `pyproject.toml`, and expose a command-line entry point so it can be run as `mytool` after installation, not just `python mytool.py`.

---

## 33.3 Packaging PowerShell Scripts as Modules

Group related functions into a `.psm1` module file plus a `.psd1` manifest, then publish to a private or public PowerShell Gallery repository so others can `Install-Module`.

---

## 33.4 Versioning

Use semantic versioning (`MAJOR.MINOR.PATCH`) and tag releases in Git:

```bash
git tag -a v1.2.0 -m "Add retry logic to API script"
git push origin v1.2.0
```

This gives users a stable reference point and makes it possible to roll back to a known-good version.

---

[Previous](./[32]-Version-Control-for-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[34]-Integrating-Scripts-with-CI-CD-Pipelines.md)
