[Previous](./[17]-DNS-and-Domain-Management.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[19]-API-Gateways.md)

*Networking & Content Delivery*

# Lesson 18 - Content Delivery Networks (CDNs)

## 18.1 What Is a CDN?

A **Content Delivery Network (CDN)** is a globally distributed network of edge servers that cache and deliver content from a location physically close to the end user, rather than every request traveling back to a single origin server. This dramatically reduces latency, cuts load on the origin server, and improves resilience against traffic spikes and some types of attacks. Examples include Amazon CloudFront, Azure CDN, Google Cloud CDN, and third-party options like Cloudflare and Fastly.

---

## 18.2 Edge Caching and Origins

A CDN sits in front of an **origin** — the actual source of the content, which could be an object storage bucket (Lesson 11), a web server, or a load balancer. The first request for a piece of content in a given region is fetched from the origin and cached at the nearest edge location; subsequent requests from nearby users are served directly from that cached edge copy, without touching the origin at all. Cache behavior is controlled by settings like **TTL (time-to-live)**, which determines how long content stays cached before the CDN re-checks the origin, and cache invalidation, which forces the CDN to fetch fresh content immediately when needed (e.g. after deploying a new version of a website).

---

## 18.3 CDN Use Cases

CDNs are used for far more than just static images and CSS files:

- **Static asset delivery** — images, JavaScript, CSS, fonts for websites.
- **Video streaming** — delivering large media files efficiently to global audiences.
- **Whole-site acceleration** — caching or optimizing dynamic content delivery.
- **API acceleration** — reducing latency for API responses that can be safely cached.
- **Security** — many CDNs bundle DDoS protection and web application firewall (WAF) features, absorbing malicious traffic at the edge before it reaches your origin.

Because a CDN reduces both latency and origin load, it's one of the highest-leverage, lowest-effort additions for any public-facing web application.

---

[Previous](./[17]-DNS-and-Domain-Management.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[19]-API-Gateways.md)
