[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Wget

Wget is a command-line utility for downloading files from the web. Compared to cURL, it's more focused on retrieving and saving content — including recursively downloading whole websites — rather than crafting arbitrary API requests.

Download: [https://www.gnu.org/software/wget/](https://www.gnu.org/software/wget/)

---

## What Is Wget?

Wget was built specifically for reliable, unattended downloading: it can resume an interrupted download, retry on failure, and follow links to mirror an entire site's structure locally. This makes it a common choice in scripts and automated backup jobs.

---

## Core Commands

```bash
wget https://example.com/file.zip     # Download a file to the current directory
wget -O output.zip https://example.com/file.zip   # Save with a specific filename
wget -c https://example.com/file.zip  # Resume a partially downloaded file
wget -b https://example.com/file.zip  # Download in the background
wget --limit-rate=200k https://example.com/file.zip  # Cap download speed
wget -r -np https://example.com/docs/  # Recursively mirror a section of a site
```

---

## cURL vs. Wget

Both fetch content over HTTP/HTTPS, but they lean toward different jobs: cURL is generally preferred for scripting API calls (custom headers, methods, request bodies), while Wget is generally preferred for straightforward file downloads and recursive site mirroring.

---

## Example Walkthrough

```bash
wget -c https://releases.example.com/app-v2.tar.gz
tar -xzvf app-v2.tar.gz
```

Downloads a release archive with resume support in case the connection drops, then extracts it once the download completes.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
