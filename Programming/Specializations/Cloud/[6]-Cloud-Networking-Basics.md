[Previous](./[5]-IAM-Users-Roles-and-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[7]-Virtual-Machines-and-Instances.md)

*Core Concepts*

# Lesson 6 - Cloud Networking Basics (VPC, Subnets, Security Groups)

## 6.1 Virtual Private Clouds

A **Virtual Private Cloud (VPC)** is an isolated, logically-defined network within the cloud provider's infrastructure, where you control the IP address range, routing, and connectivity. Think of it as your own private data center network, virtualized. You define a VPC with a CIDR block (e.g. `10.0.0.0/16`), which sets the range of private IP addresses available to resources inside it. Every resource you launch (VMs, databases, load balancers) is placed inside a VPC.

---

## 6.2 Subnets: Public vs Private

A VPC is divided into **subnets** — smaller IP ranges, typically one per Availability Zone. Subnets are classified by their internet accessibility:

- **Public subnet** — has a route to an internet gateway, so resources can have public IP addresses and be reached from the internet (e.g. web servers, load balancers).
- **Private subnet** — has no direct route to the internet; resources here (e.g. databases, internal services) can only be reached from within the VPC, adding a layer of protection.

A common pattern is placing web servers in a public subnet and databases in a private subnet, so the database is never directly internet-facing.

---

## 6.3 Security Groups and Network ACLs

Traffic into and out of resources is controlled at two layers:

- **Security Groups** — a virtual firewall attached to individual resources (like a VM), controlling inbound/outbound traffic by port, protocol, and source. They are **stateful**: if you allow inbound traffic on a port, the matching outbound response is automatically allowed.
- **Network ACLs (NACLs)** — applied at the subnet level, controlling traffic in and out of the whole subnet. They are **stateless**: inbound and outbound rules must both be defined explicitly.

A typical rule allows inbound HTTPS (port 443) from anywhere (`0.0.0.0/0`) to a web server's security group, while denying direct database access from outside the VPC.

---

[Previous](./[5]-IAM-Users-Roles-and-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[7]-Virtual-Machines-and-Instances.md)
