[Previous](./[3]-Cloud-Environment-and-CLI-Tools.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[5]-IAM-Users-Roles-and-Permissions.md)

*Core Concepts*

# Lesson 4 - Regions, Availability Zones & Global Infrastructure

## 4.1 Regions

A **region** is a geographic area (e.g. `us-east-1` in Northern Virginia, `eu-west-1` in Ireland) containing a cluster of data centers. Providers operate dozens of regions worldwide. You choose a region for each resource based on factors like proximity to your users (lower latency), data residency/compliance laws that require data to stay within a country or continent, and service/pricing availability, since not every service or price is available in every region.

---

## 4.2 Availability Zones

Each region is made up of multiple **Availability Zones (AZs)** — physically separate data centers within the region, each with independent power, cooling, and networking, but connected by low-latency links. Deploying across multiple AZs protects your application from a single data center failure: if one AZ goes down (power outage, hardware failure), traffic can fail over to another AZ in the same region with minimal disruption. This is the foundation of **high availability** design, covered in more depth in Lesson 44.

---

## 4.3 Edge Locations and Global Infrastructure

Beyond regions and AZs, providers maintain a much larger network of **edge locations** (also called points of presence) — smaller facilities located in many more cities than full regions, used to cache content and terminate connections closer to end users. These power Content Delivery Networks (Lesson 18) and DNS services (Lesson 17) by reducing the physical distance data has to travel. A useful mental model: **regions** hold your actual compute/storage resources, **AZs** provide redundancy within a region, and **edge locations** bring your content closer to users globally.

---

[Previous](./[3]-Cloud-Environment-and-CLI-Tools.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[5]-IAM-Users-Roles-and-Permissions.md)
