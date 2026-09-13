[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# cURL

cURL is a command-line tool for transferring data to or from a server using protocols like HTTP, HTTPS, FTP, and SFTP. It's the tool most developers reach for to test an API endpoint directly from the terminal.

Download: [https://curl.se/download.html](https://curl.se/download.html)

---

## What Is cURL?

Where a web browser renders a page, cURL just fetches the raw response — headers, body, status code — and prints or saves it. That makes it ideal for scripting, debugging APIs, and downloading files without a GUI.

---

## Core Commands

```bash
curl https://example.com                  # Fetch a URL and print the response body
curl -o file.html https://example.com     # Save the response to a file
curl -I https://example.com               # Fetch only the response headers
curl -X POST https://api.example.com/data \
     -H "Content-Type: application/json" \
     -d '{"name":"Ada"}'                  # Send a POST request with a JSON body
curl -u user:pass https://example.com     # Send basic auth credentials
curl -L https://example.com               # Follow redirects
```

---

## Reading a Response

By default cURL prints only the response body. Add `-i` to include the response headers above it, or `-v` for a full verbose trace of the request and response — useful when an API call isn't behaving as expected.

---

## Example Walkthrough

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://example.com
curl -X GET https://api.github.com/users/octocat -H "Accept: application/json"
```

The first command silently checks a site's HTTP status code without printing the page body — handy for uptime checks. The second fetches a public GitHub API endpoint and prints the JSON response.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
