[Previous](./[19]-Automating-Tasks-with-Python.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[21]-PowerShell-Fundamentals.md)

*Python Scripting*

# Lesson 20 - Working With APIs & HTTP Requests In Scripts

## 20.1 Making HTTP Requests

The third-party `requests` library is the standard way to call APIs from Python:

```python
import requests

response = requests.get("https://api.example.com/users", timeout=10)
response.raise_for_status()
data = response.json()
print(data)
```

Install it with `pip install requests`.

---

## 20.2 Sending Data (POST Requests)

```python
payload = {"name": "Alice", "role": "admin"}
response = requests.post("https://api.example.com/users", json=payload, timeout=10)
print(response.status_code, response.json())
```

---

## 20.3 Authentication

Most APIs require a key or token, typically sent as a header:

```python
headers = {"Authorization": f"Bearer {api_key}"}
response = requests.get("https://api.example.com/data", headers=headers)
```

Store API keys in environment variables (see Lesson 15), never hardcoded in the script.

---

## 20.4 Handling Errors and Rate Limits

```python
import time

for attempt in range(3):
    response = requests.get(url, timeout=10)
    if response.status_code == 429:   # rate limited
        time.sleep(5)
        continue
    response.raise_for_status()
    break
```

Always set a `timeout` on requests in scripts — an API call with no timeout can hang a script indefinitely.

---

[Previous](./[19]-Automating-Tasks-with-Python.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[21]-PowerShell-Fundamentals.md)
