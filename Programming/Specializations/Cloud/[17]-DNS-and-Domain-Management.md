[Previous](./[16]-Caching-Services.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[18]-Content-Delivery-Networks.md)

*Networking & Content Delivery*

# Lesson 17 - DNS & Domain Management in the Cloud

## 17.1 How DNS Works

The **Domain Name System (DNS)** translates human-readable domain names (e.g. `example.com`) into IP addresses computers use to route traffic. When a browser requests `example.com`, it queries a chain of DNS servers — a root server, then a top-level domain (TLD) server for `.com`, then the domain's **authoritative name server** — which returns the IP address to connect to. This lookup is typically cached at multiple layers (browser, OS, ISP) to speed up repeat requests.

---

## 17.2 Managed DNS Services

Cloud providers offer managed DNS hosting so you don't need to run your own name servers: AWS Route 53, Azure DNS, and Google Cloud DNS. You register or point your domain's name servers at the provider, then manage all its DNS records through their console/API. These services integrate tightly with the rest of the cloud platform — for example, Route 53 can route traffic directly to a load balancer or S3 bucket by name, and supports health-check-based failover.

---

## 17.3 Record Types and Routing Policies

Common DNS record types:

- **A record** — maps a domain to an IPv4 address.
- **AAAA record** — maps a domain to an IPv6 address.
- **CNAME record** — aliases one domain name to another.
- **MX record** — specifies mail servers for the domain.
- **TXT record** — arbitrary text, often used for domain verification or email security (SPF/DKIM).

Managed DNS services also support advanced **routing policies** beyond a simple 1:1 mapping: **weighted routing** (split traffic by percentage across endpoints, useful for gradual rollouts), **latency-based routing** (send users to the region with lowest latency), **geolocation routing** (route based on user location), and **failover routing** (automatically route to a backup if the primary endpoint's health check fails).

---

[Previous](./[16]-Caching-Services.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[18]-Content-Delivery-Networks.md)
