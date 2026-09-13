[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# nslookup

`nslookup` is a command-line tool for querying DNS (Domain Name System) records — the system that translates human-readable domain names into IP addresses. It's available on both Windows and Unix-like systems.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup](https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup)

---

## What Is nslookup?

When a website "won't load" but `ping`ing its IP address directly works fine, the problem is often DNS. `nslookup` lets you query DNS records directly to check whether a domain resolves correctly, and to which address.

---

## Core Commands

```
nslookup example.com                  # Look up the IP address for a domain
nslookup example.com 8.8.8.8          # Query a specific DNS server (Google's, here)
nslookup -type=MX example.com          # Look up mail server records
nslookup -type=TXT example.com         # Look up TXT records (often used for verification)
```

---

## Reading the Output

```
Server:  dns.google
Address:  8.8.8.8

Non-authoritative answer:
Name:    example.com
Address: 93.184.216.34
```

"Non-authoritative" simply means the answer came from a caching resolver rather than the domain's own authoritative name server — normal and expected for everyday lookups.

---

## Example Walkthrough

```
nslookup example.com
nslookup example.com 1.1.1.1
```

Looks up a domain using your default DNS server, then repeats the lookup against Cloudflare's public resolver — useful for confirming whether a DNS issue is specific to your normal resolver.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
