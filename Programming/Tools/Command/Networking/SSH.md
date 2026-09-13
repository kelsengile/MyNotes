[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# SSH (Secure Shell)

SSH lets you securely log into and run commands on a remote machine over an encrypted connection, and also underpins secure file transfer (`scp`, `sftp`) and tunneling.

Download: [https://www.openssh.com/](https://www.openssh.com/)

---

## What Is SSH?

SSH replaced older, insecure remote-login protocols (like `telnet` and `rsh`) that sent everything, including passwords, in plain text. Every SSH connection is encrypted end-to-end, and the client verifies the server's identity using a cryptographic host key, protecting against eavesdropping and man-in-the-middle attacks. Beyond interactive shells, the same encrypted channel is reused by many other tools (`scp`, `rsync -e ssh`, Git over SSH) for secure data transfer.

---

## Core Commands

```bash
ssh user@hostname                    # Connect to a remote machine
ssh -p 2222 user@hostname            # Connect on a non-default port
ssh user@hostname "ls -la"           # Run a single command remotely and exit
ssh -i ~/.ssh/my_key user@hostname   # Connect using a specific private key
exit                                  # End the SSH session
```

---

## Key-Based Authentication

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"   # Generate a new key pair
ssh-copy-id user@hostname                            # Copy your public key to a remote server
ssh user@hostname                                    # Now connects without a password
```

Key-based authentication is both more secure and more convenient than passwords: a private key never leaves your machine, and a compromised server only ever sees your public key, which is useless to an attacker on its own. `ed25519` is the modern recommended key type — smaller and faster than older RSA keys while offering equivalent or better security.

---

## The SSH Config File

```
# ~/.ssh/config
Host myserver
    HostName 203.0.113.5
    User alice
    Port 2222
    IdentityFile ~/.ssh/my_key
```

With this config, `ssh myserver` expands to the full connection details automatically — a major convenience once you're regularly connecting to several different hosts with different usernames, ports, or keys.

---

## Copying Files: scp and sftp

```bash
scp file.txt user@hostname:/remote/path/       # Copy a local file to a remote server
scp user@hostname:/remote/file.txt ./          # Copy a remote file to your local machine
scp -r folder/ user@hostname:/remote/path/     # Recursively copy a directory
sftp user@hostname                             # Open an interactive file transfer session
```

`scp` (secure copy) reuses the SSH protocol for one-off file transfers with a syntax modeled on `cp`, while `sftp` opens an interactive session supporting browsing, multiple transfers, and resuming — useful when moving many files rather than a single quick copy.

---

## Port Forwarding / Tunneling

```bash
ssh -L 8080:localhost:80 user@hostname     # Local forwarding: access remote port 80 via local 8080
ssh -R 9000:localhost:3000 user@hostname   # Remote forwarding: expose your local port 3000 on the remote side
ssh -D 1080 user@hostname                  # Dynamic forwarding: use the SSH connection as a SOCKS proxy
```

Local forwarding is commonly used to reach a service (like a database admin panel) that's only listening on `localhost` on the remote machine. Remote forwarding does the reverse — useful for exposing a local development server to a remote system temporarily. Dynamic forwarding turns SSH into a general-purpose proxy for routing arbitrary traffic through the remote host.

---

## Keeping Sessions Alive: tmux/screen and Agent Forwarding

```bash
ssh-add ~/.ssh/my_key             # Add a key to the SSH agent
ssh -A user@hostname              # Forward your local SSH agent to the remote host
```

Agent forwarding (`-A`) lets you use your local private key to authenticate to a *further* server from within an SSH session, without copying the key itself onto the intermediate machine — useful for jumping through a bastion host to reach internal servers.

---

## Common Gotchas

- Known hosts warnings: SSH stores a fingerprint of every server you've connected to in `~/.ssh/known_hosts`; if a server's key ever changes unexpectedly, SSH refuses to connect and warns loudly, since this is exactly the signature of a potential man-in-the-middle attack — don't blindly clear this warning without understanding why the key changed.
- Permission errors on keys: SSH refuses to use a private key file with overly permissive file permissions (`chmod 600 ~/.ssh/id_ed25519` is the expected setting) as a safeguard against other users on the same machine reading it.

---

## Example Walkthrough

```bash
ssh-keygen -t ed25519
ssh-copy-id user@203.0.113.5
ssh user@203.0.113.5
```

Generates a new key pair, installs the public key on a remote server, and then connects without needing to type a password — the standard first-time setup for secure remote access.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/dig.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# dig (Domain Information Groper)

`dig` queries DNS servers directly and displays detailed information about the results, making it the standard tool for diagnosing DNS problems and inspecting how a domain is configured.

Download: [https://www.isc.org/bind/](https://www.isc.org/bind/)

---

## What Is dig?

`dig` sends a raw DNS query and shows exactly what came back — the answer, which server answered, how long it took, and every relevant DNS record — rather than abstracting the lookup away like a browser or `ping` would. This makes it the preferred tool for diagnosing DNS-specific issues: is the domain pointing to the right IP, is a particular record type missing, is a specific DNS server misbehaving.

---

## Core Commands

```bash
dig example.com                    # Query the A record (IPv4 address) for a domain
dig example.com MX                 # Query mail server (MX) records
dig example.com NS                 # Query nameserver records
dig example.com TXT                # Query TXT records (often used for verification/SPF)
dig example.com ANY                # Query all record types (deprecated by many servers now)
dig +short example.com             # Print just the answer, no extra formatting
dig @8.8.8.8 example.com           # Query a specific DNS server directly
```

---

## Understanding the Output

```
;; QUESTION SECTION:
;example.com.                  IN      A

;; ANSWER SECTION:
example.com.            300     IN      A       93.184.216.34

;; AUTHORITY SECTION:
;; ADDITIONAL SECTION:

;; Query time: 24 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
```

The **ANSWER SECTION** is usually what you care about — here, a 300-second TTL (how long resolvers should cache this) and the resolved IP. **Query time** and **SERVER** show how long the lookup took and which resolver actually answered it, useful when comparing your local resolver against a public one like `8.8.8.8`.

---

## Common Record Types

| Type | Meaning |
|---|---|
| `A` | Maps a domain to an IPv4 address |
| `AAAA` | Maps a domain to an IPv6 address |
| `CNAME` | Alias pointing to another domain name |
| `MX` | Mail server(s) responsible for a domain |
| `NS` | Authoritative nameservers for a domain |
| `TXT` | Arbitrary text, often used for domain verification, SPF, or DKIM |
| `SOA` | Start of Authority — administrative info about a DNS zone |
| `PTR` | Reverse lookup, mapping an IP back to a hostname |

---

## Reverse DNS Lookups

```bash
dig -x 93.184.216.34
```

`-x` performs a reverse lookup, finding the hostname associated with a given IP address — useful for identifying what a mysterious IP in a log file actually belongs to.

---

## Tracing the Full Resolution Path

```bash
dig +trace example.com
```

`+trace` follows the DNS resolution process from the root nameservers down through each delegation, showing exactly which server is authoritative at each step — the deepest level of DNS debugging, useful when a domain resolves incorrectly and you need to find exactly which nameserver in the chain is returning the wrong answer.

---

## Comparing Different DNS Servers

```bash
dig @1.1.1.1 example.com +short
dig @8.8.8.8 example.com +short
```

If a domain resolves differently depending on which DNS server answers, that's a strong sign of either DNS propagation still in progress after a recent change, or a DNS server serving stale cached data.

---

## Common Gotchas

- Caching and propagation delay: DNS changes can take time to propagate globally due to caching at intermediate resolvers — `dig` against a specific authoritative nameserver (found via an `NS` query) shows the "true" current answer, bypassing any stale cache elsewhere.
- `+short` vs full output: scripts almost always want `+short` for easy parsing, while full output is more useful for a human diagnosing a problem, since it includes TTLs and query metadata.

---

## Example Walkthrough

```bash
dig example.com +short
dig example.com MX +short
dig -x 93.184.216.34
```

Checks a domain's IP address, its mail server configuration, and then reverse-resolves that IP back to a hostname — a typical sequence when auditing a domain's DNS setup.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)