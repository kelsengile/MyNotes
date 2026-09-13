[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# dig (Domain Information Groper)

`dig` is a DNS lookup utility for Unix-like systems, generally preferred over `nslookup` for troubleshooting because it exposes far more detail about a DNS response by default.

Download: [https://bind9.readthedocs.io/en/latest/manpages.html](https://bind9.readthedocs.io/en/latest/manpages.html)

---

## What Is dig?

`dig` queries DNS servers directly and prints the full response — including the query section, answer section, and timing — which makes it a favorite among network administrators for diagnosing DNS problems precisely.

---

## Core Commands

```bash
dig example.com                 # Look up A records (IPv4 address) for a domain
dig example.com MX              # Look up mail server records
dig example.com +short          # Print just the answer, no extra detail
dig @8.8.8.8 example.com        # Query a specific DNS server
dig -x 93.184.216.34             # Reverse lookup: IP address to hostname
```

---

## Reading the Output

```
;; ANSWER SECTION:
example.com.   86400   IN   A   93.184.216.34
```

`86400` is the record's TTL in seconds (how long resolvers should cache it), `IN` means "internet class," and `A` is the record type — here, a plain IPv4 address mapping.

---

## Example Walkthrough

```bash
dig example.com +short
dig example.com MX +short
```

Quickly checks a domain's IP address and its mail server records, using `+short` to skip the verbose header and footer output — handy when scripting or just want the answer fast.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
