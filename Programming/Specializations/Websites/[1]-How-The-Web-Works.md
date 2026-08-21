 [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[2]-Development-Environment.md)

*Getting Started*

# Lesson 1 - How The Web Works

## 1.1 Clients and Servers

The web runs on a **client-server model**. A **client** (usually a browser like Chrome or Firefox) requests a resource — a web page, an image, a piece of data — and a **server** (a computer running software that listens for those requests) sends a response back. This request/response cycle happens constantly as you browse: every link click, form submission, and page refresh triggers a new round trip between client and server.

---

## 1.2 URLs and DNS

A **URL** (Uniform Resource Locator) is the address of a resource, e.g. `https://www.example.com/products/shoes?color=red`. It breaks down into a scheme (`https`), a host/domain (`www.example.com`), a path (`/products/shoes`), and a query string (`?color=red`).

Domains are human-friendly names, but computers route traffic using IP addresses (e.g. `93.184.216.34`). The **Domain Name System (DNS)** is the internet's phonebook: it translates domain names into IP addresses. When you type a URL, your browser asks a DNS resolver to look up the IP address for that domain before it can connect to anything.

---

## 1.3 HTTP and HTTPS

**HTTP** (HyperText Transfer Protocol) is the language clients and servers speak to exchange resources. A request has a **method** (`GET`, `POST`, `PUT`, `DELETE`, etc.), a URL, headers (metadata like content type or authorization), and sometimes a body (data being sent). A response has a **status code** (`200 OK`, `404 Not Found`, `500 Internal Server Error`), headers, and usually a body (the HTML, JSON, or file being returned).

**HTTPS** is HTTP layered over **TLS (Transport Layer Security)**, which encrypts traffic between client and server so it can't be read or tampered with in transit. Modern browsers flag non-HTTPS sites as "Not Secure," and HTTPS is effectively required for production websites today.

---

## 1.4 Rendering a Page

Once the browser receives an HTML response, it doesn't just display raw text — it builds a **DOM (Document Object Model)**, a tree-like in-memory representation of the page's elements. It parses linked CSS into a **CSSOM** (a styling tree), combines the two into a **render tree**, calculates layout (where everything goes), and finally **paints** pixels to the screen. Any linked JavaScript is fetched and executed, often modifying the DOM further, which can trigger the browser to re-layout and re-paint.

---

## 1.5 Static vs Dynamic Content

A **static site** serves the exact same file for every visitor — a plain HTML file sitting on a server. A **dynamic site** generates content on the fly, often per-request, by running server-side code that queries a database, applies logic, and produces custom HTML or data (commonly JSON) for that specific request. Most real-world websites are dynamic to some degree, even if they cache aggressively to feel as fast as static ones.

---

[Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[2]-Development-Environment.md)
