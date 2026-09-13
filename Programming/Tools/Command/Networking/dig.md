[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# dig (Domain Information Groper)

`dig` queries DNS (Domain Name System) servers directly and shows the raw response, making it the standard tool for diagnosing DNS problems and understanding exactly how a domain name resolves.

Reference: [https://linux.die.net/man/1/dig](https://linux.die.net/man/1/dig)

---

## What Is dig?

DNS is the distributed, hierarchical system that translates human-readable names (`example.com`) into IP addresses and other records computers actually use. Every time you visit a website, your computer performs one or more DNS lookups behind the scenes — usually invisibly, through your operating system's resolver. `dig` bypasses that abstraction and lets you send a DNS query yourself, to any server you choose, and see the exact response — including timing, the responding server, flags, and every record returned — rather than just the final answer an application would use.

`dig` is part of the BIND DNS software suite and is available by default on most Linux distributions and macOS; on Windows it's typically installed separately or accessed via WSL (the built-in equivalent is `nslookup`, though it's less detailed).

---

## Core Commands

```bash
dig example.com                     # Basic A record lookup
dig example.com MX                  # Look up mail exchange records
dig example.com NS                  # Look up authoritative name servers
dig example.com TXT                 # Look up TXT records (SPF, verification, etc.)
dig example.com ANY                 # Ask for all record types (often restricted by servers now)
dig @8.8.8.8 example.com            # Query a specific DNS server (Google's public resolver here)
dig +short example.com              # Just the answer, no extra formatting
dig +trace example.com              # Trace the full resolution path from the root servers down
dig -x 93.184.216.34                # Reverse lookup: IP address to hostname
dig example.com +noall +answer      # Show only the answer section
```

## Common Flags

| Flag | Meaning |
|---|---|
| `@SERVER` | Query this specific DNS server instead of the system default |
| `+short` | Minimal, single-line output |
| `+trace` | Follow the full delegation chain from root → TLD → authoritative server |
| `+noall +answer` | Suppress everything except the answer section |
| `-x` | Reverse DNS lookup (PTR record) |
| `+stats` / `+nostats` | Show/hide query statistics footer |
| `+dnssec` | Request DNSSEC validation records (RRSIG, etc.) |
| `-t TYPE` | Explicitly specify a record type |
| `+tcp` | Force the query over TCP instead of UDP |

---

## Understanding dig's Output

A typical response has several sections:

- **HEADER** — status flags like `NOERROR`/`NXDOMAIN`, query/answer/authority counts.
- **QUESTION SECTION** — restates what was asked.
- **ANSWER SECTION** — the actual record(s) returned, with a TTL (time-to-live, in seconds) telling resolvers how long they may cache the result.
- **AUTHORITY SECTION** — which name servers are authoritative for the domain, shown especially when there's no direct answer.
- **ADDITIONAL SECTION** — extra helpful records, like the IP addresses of the name servers listed above.

---

## Common DNS Record Types dig Can Query

| Type | Purpose |
|---|---|
| `A` | Maps a name to an IPv4 address |
| `AAAA` | Maps a name to an IPv6 address |
| `CNAME` | Alias pointing to another name |
| `MX` | Mail server(s) for a domain, with priority |
| `NS` | Authoritative name servers for a domain |
| `TXT` | Arbitrary text — commonly used for SPF, DKIM, domain verification |
| `SOA` | Start of Authority — zone's admin contact, serial number, refresh timers |
| `PTR` | Reverse mapping, IP address to name |
| `CAA` | Which Certificate Authorities may issue TLS certs for the domain |
| `SRV` | Service location records (e.g., for SIP, XMPP) |

---

## Diagnosing Real Problems with dig

- **"Is my DNS change live yet?"** — `dig +trace` or querying a specific public resolver (`@1.1.1.1`) shows whether a record has actually propagated, independent of what might be cached locally.
- **Mail delivery issues** — checking `MX` and `TXT` (SPF/DKIM/DMARC) records to confirm a domain is configured correctly to send/receive email.
- **Comparing resolvers** — querying `@8.8.8.8` vs. `@1.1.1.1` vs. your ISP's resolver to spot inconsistent or stale caching.
- **CDN/load-balancer verification** — confirming a domain resolves to the expected set of IPs after a CDN or DNS provider migration.
- **DNSSEC validation debugging** — `+dnssec` surfaces signature records so you can confirm a zone's DNSSEC chain is intact.

---

## Related Tools

- `nslookup` — an older, interactive DNS lookup tool; less detailed output but present on virtually every OS including plain Windows.
- `host` — a simpler, terser DNS lookup command, good for quick scripted checks.
- `whois` — looks up domain registration data (owner, registrar, expiry), a different layer from DNS resolution itself.
- `resolvectl` / `systemd-resolve` — inspect the DNS resolver configuration and cache on modern Linux systems.

---

## Example Walkthrough

```bash
dig example.com +short
dig example.com MX +short
dig @1.1.1.1 example.com +short
```

Quickly checks what IP a domain currently resolves to, what mail servers handle its email, and whether a different public DNS resolver (Cloudflare's `1.1.1.1`) returns the same answer — a fast triage sequence for "is this a DNS problem?"

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)