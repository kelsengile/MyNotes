[Previous](./[3]-Virtual-Environments-and-Pip.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Environment Variables & Configuration

## 4.1 What Are Environment Variables

Environment variables are key-value pairs stored outside your code, at the operating-system level, that programs can read at runtime. They're commonly used to store configuration that changes between environments (development, testing, production) — such as database URLs, API keys, and debug flags — without hardcoding them into your source files or committing secrets to version control.

---

## 4.2 Reading Env Vars with os.environ

Python's built-in `os` module gives access to environment variables through `os.environ`:

```python
import os

# Raises KeyError if not set
database_url = os.environ["DATABASE_URL"]

# Safer: returns None (or a default) if not set
api_key = os.environ.get("API_KEY")
debug_mode = os.environ.get("DEBUG", "False")
```

You can set a variable temporarily for a single command in the terminal:

```bash
# macOS / Linux
export API_KEY="abc123"

# Windows (PowerShell)
$env:API_KEY = "abc123"
```

---

## 4.3 .env files and python-dotenv

Typing `export` commands every time is tedious and easy to forget. Instead, developers commonly store variables in a `.env` file at the project root:

```
DATABASE_URL=postgresql://localhost/mydb
API_KEY=abc123
DEBUG=True
```

The third-party [`python-dotenv`](https://pypi.org/project/python-dotenv/) package loads this file into `os.environ` automatically:

```bash
pip install python-dotenv
```

```python
from dotenv import load_dotenv
import os

load_dotenv()  # reads .env in the current directory

api_key = os.environ.get("API_KEY")
```

---

## 4.4 Best Practices for Secrets & Config

- **Never commit `.env` files** containing real secrets to version control — add `.env` to your `.gitignore`.
- Commit a `.env.example` file instead, listing the required variable names with placeholder values, so other developers know what to set up.
- Keep configuration and code separate: values that differ between environments (URLs, keys, feature flags) belong in environment variables, not hardcoded constants.
- Use different values per environment (development, staging, production) rather than one shared set of credentials.

[Previous](./[3]-Virtual-Environments-and-Pip.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[5]-Variables-and-Data-Types.md)
