*Data Collection & Cleaning*

# Lesson 9 - Web Scraping & APIs for Data Collection

[Previous](./[8]-Reading-Data-from-Files-and-Databases.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[10]-Data-Cleaning-and-Handling-Missing-Values.md)

---

## 9.1 Calling Web APIs

An **API** (Application Programming Interface) lets you request data directly from a service in a structured format (usually JSON), without needing to parse HTML.

```python
import requests

response = requests.get("https://api.example.com/weather", params={"city": "Manila"})
data = response.json()
print(data["temperature"])
```

Always check `response.status_code` (200 means success) and read the API's documentation for rate limits and required authentication (often an API key passed in headers or params).

---

## 9.2 Web Scraping Basics

When no API is available, **web scraping** extracts data directly from a webpage's HTML using libraries like `BeautifulSoup`:

```python
from bs4 import BeautifulSoup
import requests

html = requests.get("https://example.com/products").text
soup = BeautifulSoup(html, "html.parser")

titles = soup.find_all("h2", class_="product-title")
for t in titles:
    print(t.get_text())
```

For pages that load content dynamically with JavaScript, tools like `Selenium` or `Playwright` simulate a real browser to render the page before scraping it.

---

## 9.3 Scraping Ethics and Legality

Before scraping a site:

- Check its `robots.txt` file (e.g. `example.com/robots.txt`) for which pages are off-limits to automated tools.
- Read the site's Terms of Service — many explicitly prohibit scraping.
- Rate-limit your requests (add delays) so you don't overload the server.
- Prefer an official API when one exists — it's more stable and explicitly permitted.

---

## 9.4 Storing Collected Data

Once collected, save data immediately in a durable format rather than re-fetching it each time you run your analysis:

```python
import pandas as pd

records = [{"title": t.get_text()} for t in titles]
df = pd.DataFrame(records)
df.to_csv("scraped_products.csv", index=False)
```

This also protects you if the source website changes or removes the content later.

---

[Previous](./[8]-Reading-Data-from-Files-and-Databases.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[10]-Data-Cleaning-and-Handling-Missing-Values.md)
