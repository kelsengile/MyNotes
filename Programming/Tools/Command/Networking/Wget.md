[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Wget

Wget ("web get") is a free, command-line utility for retrieving files over HTTP, HTTPS, and FTP. Compared to cURL, it's more focused on retrieving and saving content — including recursively downloading whole websites — rather than crafting arbitrary API requests.

Download: [https://www.gnu.org/software/wget/](https://www.gnu.org/software/wget/)

---

## What Is Wget?

Wget was written in 1996 (originally called "Geturl") as part of the GNU Project, and has since become a standard tool on nearly every Linux distribution. It was built specifically for reliable, unattended downloading:

- It can **resume** an interrupted download rather than starting over.
- It **retries automatically** on network failure or timeout.
- It can run entirely **non-interactively**, making it well suited to cron jobs, scripts, and servers with no GUI or active terminal session (`wget` doesn't need a terminal to stay open — see `-b` and `nohup` below).
- It can **follow links** to mirror an entire site's structure locally, rewriting links for offline browsing.

Because of this, Wget is common in backup scripts, CI pipelines, embedded systems, and anywhere a script needs to fetch a file (or a tree of files) without human supervision.

---

## Installation

Wget ships by default on most Linux distributions. Elsewhere:

```bash
# Debian/Ubuntu
sudo apt install wget

# Fedora
sudo dnf install wget

# macOS (Homebrew) — not installed by default on macOS
brew install wget

# Windows
# Available via GnuWin32, Chocolatey (choco install wget), or WSL
```

---

## Core Commands

```bash
wget https://example.com/file.zip                 # Download a file to the current directory
wget -O output.zip https://example.com/file.zip    # Save with a specific filename
wget -c https://example.com/file.zip               # Resume a partially downloaded file
wget -b https://example.com/file.zip               # Download in the background
wget -q https://example.com/file.zip               # Quiet mode — no progress output
wget --limit-rate=200k https://example.com/file.zip  # Cap download speed
wget -r -np https://example.com/docs/              # Recursively mirror a section of a site
wget -i urls.txt                                   # Download every URL listed in a text file
```

---

## Retrying and Resilience

Wget's core selling point is that it assumes the network will fail and plans for it.

```bash
wget --tries=10 https://example.com/file.zip        # Retry up to 10 times (default: 20)
wget --tries=0 https://example.com/file.zip          # Retry forever
wget --timeout=30 https://example.com/file.zip       # Give up on a stalled connection after 30s
wget --waitretry=5 https://example.com/file.zip      # Wait 5s (increasing) between retries
wget --retry-connrefused https://example.com/file.zip  # Also retry if the connection is refused
```

Combined with `-c` (continue), this is why Wget is often chosen over a browser download for large files on unstable connections — a dropped connection just means Wget quietly picks up where it left off.

---

## Recursive Downloading and Mirroring

This is Wget's most distinctive feature: it can walk a site's link structure and download it locally, like a basic web crawler.

```bash
wget -r https://example.com/                # Recursive download (default depth: 5)
wget -r -l 2 https://example.com/           # Limit recursion to 2 levels deep
wget -r -np https://example.com/docs/        # -np ("no parent") stays within /docs/, won't climb up
wget -r -nd https://example.com/images/      # -nd ("no directories") saves all files flat
wget -m https://example.com/                 # --mirror: shorthand for recursive + timestamping + infinite depth
wget -m -k -p https://example.com/           # Mirror a site for offline browsing
```

- `-k` / `--convert-links`: rewrites downloaded pages so links point to the local copies instead of the live site.
- `-p` / `--page-requisites`: also grabs everything needed to render a page correctly (images, CSS, JS), even if those files fall outside the recursion depth.
- `-A` / `--accept` and `-R` / `--reject`: filter recursive downloads by file extension, e.g. `-A jpg,png` or `-R "*.pdf"`.
- `--domains=example.com`: restrict a crawl to a specific domain, useful when a site links off-site heavily.

This combination (`wget -m -k -p -np`) is the classic recipe for archiving a small website for offline reading.

---

## Authentication, Headers, and Cookies

```bash
wget --user=alice --password=secret https://example.com/private.zip   # HTTP basic auth
wget --header="Authorization: Bearer TOKEN" https://api.example.com/file
wget --user-agent="Mozilla/5.0" https://example.com/file.zip   # Spoof a browser User-Agent
wget --load-cookies cookies.txt https://example.com/           # Reuse a browser's exported cookies
wget --save-cookies cookies.txt --keep-session-cookies https://example.com/login
wget --post-data="user=alice&pass=secret" https://example.com/login   # Submit a login form
```

Sites that gate content behind a login often need cookies exported from a real browser session (via an extension like "Get cookies.txt") before Wget can fetch protected pages.

---

## Networking Options

```bash
wget -e use_proxy=yes -e http_proxy=proxy.example.com:8080 https://example.com/file.zip
wget --no-check-certificate https://self-signed.example.com/file.zip   # Skip TLS verification (use with caution)
wget --bind-address=192.168.1.5 https://example.com/file.zip           # Choose a specific local interface
wget -4 https://example.com/file.zip     # Force IPv4
wget -6 https://example.com/file.zip     # Force IPv6
```

---

## Logging and Output Control

```bash
wget -v https://example.com/file.zip     # Verbose (default)
wget -nv https://example.com/file.zip    # Non-verbose — errors and basic info only
wget -q https://example.com/file.zip     # Fully quiet
wget -o log.txt https://example.com/file.zip    # Redirect all output to a log file
wget --spider https://example.com/file.zip      # Check a URL exists without downloading it (link checking)
wget --show-progress https://example.com/file.zip  # Force the progress bar even with -q logging elsewhere
```

`--spider` mode is frequently used in scripts and monitoring tools just to verify a URL is reachable (returns a non-zero exit code if not) without wasting bandwidth on the actual file.

---

## The `.wgetrc` Configuration File

Wget reads default options from `/etc/wgetrc` (system-wide) and `~/.wgetrc` (per-user), so repeated flags can be set once instead of typed every time:

```
# ~/.wgetrc
tries = 10
timeout = 30
user_agent = Mozilla/5.0
limit_rate = 500k
```

Any option available on the command line has a corresponding config-file directive (usually the long-option name with dashes replaced by underscores).

---

## Rate Limiting and Politeness

When crawling a site recursively, Wget can be told to behave politely so it doesn't hammer a server:

```bash
wget --limit-rate=100k -r https://example.com/
wget --wait=2 -r https://example.com/           # Wait 2 seconds between requests
wget --random-wait -r https://example.com/       # Randomize the wait time (0.5x–1.5x) to look less bot-like
```

---

## cURL vs. Wget

Both fetch content over HTTP/HTTPS, but they lean toward different jobs:

| | cURL | Wget |
|---|---|---|
| Primary use case | Scripting API calls | Downloading files, mirroring sites |
| Protocols | HTTP(S), FTP, and dozens more (SMTP, IMAP, LDAP, etc.) | HTTP(S), FTP |
| Recursive downloads | No | Yes (`-r`, `-m`) |
| Request customization | Very flexible (methods, headers, bodies) | Basic (headers, POST data) |
| Output default | Prints to stdout | Saves to a file |
| Library form | libcurl is embeddable in other programs | No equivalent embeddable library |

In short: reach for cURL when scripting an API call with custom methods/headers/bodies; reach for Wget when the job is "get this file (or this whole section of a site) reliably and save it."

---

## Related Tools

- **curl** — the general-purpose counterpart described above.
- **aria2** — a lightweight download utility supporting multi-connection/multi-source downloads (HTTP, FTP, BitTorrent, Metalink) — often faster than Wget for large single files because it splits them into parallel segments.
- **httrack** — a dedicated website-mirroring tool with more fine-grained crawl controls than `wget -m`.
- **rsync** — better suited than Wget when both endpoints support it, since it only transfers the parts of a file that changed.

---

## Example Walkthrough

```bash
wget -c https://releases.example.com/app-v2.tar.gz
tar -xzvf app-v2.tar.gz
```

Downloads a release archive with resume support in case the connection drops, then extracts it once the download completes.

```bash
wget -m -k -p -np --wait=1 https://docs.example.com/guide/
```

Mirrors a documentation section for offline reading: recursive download, converted links, all page assets included, staying under `/guide/`, with a one-second pause between requests to avoid hammering the server.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)