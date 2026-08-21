[Previous](./[28]-Hosting-and-Deploying.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[30]-CI-CD-for-Web-Projects.md)

*Deployment & Production*

# Lesson 29 - Domains, DNS & HTTPS

## 29.1 Buying and Owning a Domain

A **domain name** (e.g. `example.com`) is registered through a **domain registrar** (Namecheap, Google Domains, GoDaddy) for a recurring fee. Registering a domain doesn't host anything by itself — it just reserves the name and lets you control where it points, via DNS.

---

## 29.2 DNS Records

Recall from Lesson 1 that DNS maps domain names to IP addresses. This mapping is controlled through **DNS records** configured at your registrar or DNS provider:

- **A record** — points a domain directly to an IPv4 address
- **CNAME record** — points a domain to another domain name (common for pointing to a hosting platform, e.g. `www` → `your-site.netlify.app`)
- **MX record** — routes email for the domain to a mail server
- **TXT record** — arbitrary text, often used to verify domain ownership

Changes to DNS records take time to **propagate** across the internet's many DNS servers, sometimes up to 24–48 hours, because of caching (**TTL**, or time-to-live) at each level.

---

## 29.3 Connecting a Domain to a Host

Most hosting platforms provide instructions for pointing a custom domain at them — usually adding a specific A or CNAME record. Once DNS resolves correctly, requests to your domain reach your host's servers exactly as described in Lesson 1's client-server flow.

---

## 29.4 HTTPS and Certificates

Recall from Lesson 1 that HTTPS relies on TLS encryption, which requires a **TLS/SSL certificate** proving a server's identity for a given domain. Certificates used to require manual purchase and renewal; today, services like **Let's Encrypt** issue free certificates automatically, and most modern hosting platforms provision and renew HTTPS certificates for connected custom domains without any manual steps.

---

## 29.5 Subdomains

A **subdomain** (`blog.example.com`, `api.example.com`) is a prefix on a domain, often pointing to an entirely different service or server than the root domain. Subdomains are commonly used to separate a marketing site from an app, or a front end from its API, each potentially hosted and deployed independently.

---

[Previous](./[28]-Hosting-and-Deploying.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[30]-CI-CD-for-Web-Projects.md)
