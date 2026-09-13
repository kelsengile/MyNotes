[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cURL

`curl` transfers data to or from a server using a URL, supporting an enormous range of protocols (HTTP, HTTPS, FTP, and many more). It's the standard command-line tool for testing APIs, downloading files, and scripting web requests.

Download: [https://curl.se/](https://curl.se/)

---

## What Is cURL?

`curl` is built as a general-purpose, scriptable client for URL-based transfers — it does one request per invocation and prints the result to stdout by default, which makes it easy to pipe into other tools (`jq`, `grep`, a file) as part of a larger pipeline. This composability is why `curl` is the default choice for interacting with web APIs from scripts and CI pipelines, rather than a full GUI client like Postman.

---

## Core Commands

```bash
curl https://example.com                  # GET a URL, print the response body
curl -o file.html https://example.com     # Save output to a named file
curl -O https://example.com/file.zip      # Save using the remote file's own name
curl -I https://example.com               # Fetch headers only (HEAD request)
curl -L https://example.com               # Follow redirects
curl -s https://example.com               # Silent mode — hide progress meter
```

---

## Making Different HTTP Requests

```bash
curl -X POST https://api.example.com/users              # Explicit POST
curl -X PUT https://api.example.com/users/1              # PUT
curl -X DELETE https://api.example.com/users/1           # DELETE
curl -d "name=Alice&age=30" https://api.example.com/users  # POST with form data (implies -X POST)
curl -d '{"name":"Alice"}' -H "Content-Type: application/json" https://api.example.com/users  # POST JSON
```

`-d`/`--data` automatically switches the method to POST unless another method is explicitly specified, and sends the given data as the request body.

---

## Headers and Authentication

```bash
curl -H "Authorization: Bearer TOKEN" https://api.example.com/data
curl -H "Content-Type: application/json" -H "Accept: application/json" https://api.example.com
curl -u username:password https://api.example.com     # HTTP Basic auth
```

`-H` can be repeated for as many custom headers as a request needs, and `-u` provides Basic authentication credentials without manually building the `Authorization` header.

---

## Uploading Files

```bash
curl -F "file=@photo.jpg" https://api.example.com/upload    # Multipart form file upload
curl -T bigfile.zip https://example.com/upload/bigfile.zip  # Direct PUT upload of a file
```

`-F` builds a proper `multipart/form-data` request (the same format an HTML `<form>` with a file input would send), while `-T` uploads a file's raw contents directly as the request body, typically used with PUT-based upload APIs.

---

## Inspecting Requests and Responses

```bash
curl -v https://example.com          # Verbose — show the full request/response including headers
curl -i https://example.com          # Include response headers in the output, before the body
curl -w "%{http_code}\n" -o /dev/null -s https://example.com  # Print just the HTTP status code
```

`-v` is the go-to flag for debugging why a request isn't behaving as expected, since it shows the exact headers sent and received, the TLS handshake, and redirect chain. `-w` with format variables lets scripts extract specific metadata (status code, timing, size) without parsing the full response.

---

## Handling Cookies and Sessions

```bash
curl -c cookies.txt https://example.com/login -d "user=alice&pass=secret"  # Save cookies
curl -b cookies.txt https://example.com/dashboard                           # Send saved cookies back
```

`-c` writes any cookies set by the server to a file, and `-b` sends cookies from a file back on a subsequent request — a way to maintain a login session across multiple `curl` invocations without a full browser.

---

## Retry and Timeout Behavior

```bash
curl --retry 3 --retry-delay 2 https://api.example.com
curl --max-time 10 https://api.example.com
curl --connect-timeout 5 https://api.example.com
```

`--retry` is useful in scripts hitting flaky endpoints; `--max-time` bounds the entire operation, while `--connect-timeout` bounds only the initial connection phase — distinguishing "the server is unreachable" from "the server is just slow to respond."

---

## Common Gotchas

- Method confusion: `-d` silently switches a request to POST, which can be surprising if you intended a GET with query parameters — use `-G` with `-d` to force the data to be appended as URL query parameters instead.
- Self-signed certificates: `-k`/`--insecure` disables TLS certificate verification entirely, which is convenient for local testing but should never be used against production endpoints, since it defeats the protection HTTPS is meant to provide.

---

## Example Walkthrough

```bash
curl -s -X POST https://api.example.com/login \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"secret"}' \
  -w "\nStatus: %{http_code}\n"
```

Sends a JSON login request with the right content type, suppresses the progress meter, and prints the resulting HTTP status code — a typical pattern for testing an API endpoint from the command line.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/Wget.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Wget

Wget is a command-line tool specialized for downloading files over HTTP, HTTPS, and FTP, with strong support for retrying, resuming, and recursively mirroring entire websites.

Download: [https://www.gnu.org/software/wget/](https://www.gnu.org/software/wget/)

---

## What Is Wget?

Where `curl` is a general-purpose data-transfer tool built to work well inside scripts and pipelines, `wget` is purpose-built for the specific job of reliably fetching and saving files, especially over unstable connections. It's designed to run non-interactively, which is why it was historically the tool of choice for downloading files in a script or a `cron` job left running unattended, and why it defaults to writing files to disk rather than stdout.

---

## Core Commands

```bash
wget https://example.com/file.zip           # Download a file, keeping its remote name
wget -O myname.zip https://example.com/file.zip  # Download and save under a specific name
wget -c https://example.com/bigfile.iso      # Continue/resume a partially downloaded file
wget -b https://example.com/file.zip         # Download in the background
wget -q https://example.com/file.zip         # Quiet mode — suppress output
wget --limit-rate=200k https://example.com/file.zip  # Cap download speed
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-O file` | Save output under a specific filename |
| `-c` | Continue (resume) a partially downloaded file |
| `-b` | Run in the background |
| `-q` | Quiet — suppress progress and status messages |
| `-P dir` | Save downloaded files into a specific directory |
| `--limit-rate=N` | Cap bandwidth usage |
| `-t N` | Number of retries on failure (0 for infinite) |
| `--timeout=N` | Set timeout for the whole operation |
| `-nc` | No-clobber — skip download if the file already exists locally |

---

## Resuming Interrupted Downloads

```bash
wget -c https://example.com/large-file.iso
```

`-c` tells `wget` to check how much of the file already exists locally and request only the remaining bytes from the server (assuming the server supports HTTP range requests) — essential for large downloads over unreliable connections, since it avoids restarting from zero after a dropped connection.

---

## Mirroring Websites Recursively

```bash
wget --mirror --convert-links --page-requisites --no-parent https://example.com/docs/
```

This combination is the classic "download a whole website for offline use" recipe: `--mirror` recursively follows links, `--page-requisites` also grabs images/CSS/JS needed to render pages correctly, `--convert-links` rewrites links so they work locally, and `--no-parent` prevents wandering outside the specified directory into the rest of the site.

---

## Recursive Download Options

| Flag | Meaning |
|---|---|
| `-r` | Recursive download, following links |
| `-l N` | Maximum recursion depth (default 5) |
| `--mirror` | Shortcut for recursive mirroring with sensible defaults |
| `--no-parent` | Don't ascend to the parent directory when recursing |
| `-A pattern` | Only accept files matching a pattern (e.g. `*.pdf`) |
| `-R pattern` | Reject files matching a pattern |

---

## Batch Downloading From a List

```bash
wget -i urls.txt
```

`-i` reads a list of URLs from a file, one per line, and downloads each in turn — useful for downloading many files without writing a shell loop.

---

## Authentication and Headers

```bash
wget --user=alice --password=secret https://example.com/protected/file.zip
wget --header="Authorization: Bearer TOKEN" https://api.example.com/data
```

---

## Common Gotchas

- Overwriting vs resuming: without `-c`, re-running `wget` on a partially downloaded file either restarts the download from scratch or appends a `.1` suffix to the new file, depending on settings — `-c` is what you want for genuinely resuming.
- Recursive downloads without limits: `-r` without `-l` or `--no-parent` can wander much further across a site (or the wider internet, if external links are followed) than intended.

---

## Example Walkthrough

```bash
wget -c -P ~/Downloads https://example.com/dataset.tar.gz
```

Downloads a large dataset into a specific folder, resuming automatically if the connection drops partway through — a typical pattern for fetching large files unattended.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)