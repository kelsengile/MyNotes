[Previous](./[8]-Process-And-Task-Management.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[10]-Disk-And-Drive-Management.md)

# Lesson 9 - Networking Commands

## 9.1 Viewing your network configuration

```
ipconfig
```
Shows your IP address, subnet mask, and default gateway for each network adapter.

```
ipconfig /all
```
Adds MAC addresses, DNS servers, DHCP details, and more.

---

## 9.2 Renewing your IP address

If your internet connection is misbehaving, these two in sequence are a classic fix:
```
ipconfig /release
ipconfig /renew
```

---

## 9.3 Flushing the DNS cache

```
ipconfig /flushdns
```
Clears cached DNS lookups — useful if a website recently changed servers and your computer is still using an old address.

---

## 9.4 Testing if a host is reachable

```
ping google.com
```
Sends a handful of test packets and reports whether they came back, and how long it took. Great first step when troubleshooting "is the internet down?"

```
ping -t google.com
```
Pings continuously until you stop it with `Ctrl + C` — handy for watching a connection over time.

---

## 9.5 Tracing the path to a host

```
tracert google.com
```
Shows every network hop (router) between you and the destination, with timing for each — useful for spotting *where* a connection is slow or failing.

---

## 9.6 Viewing active connections

```
netstat
```
Lists active network connections.

```
netstat -an
```
Shows all connections and listening ports, with addresses/ports shown as numbers rather than resolved names (faster, and useful for checking what's listening on your machine).

---

## 9.7 Looking up a domain's IP address

```
nslookup google.com
```
Shows the IP address(es) a domain name resolves to.

---

[Previous](./[8]-Process-And-Task-Management.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[10]-Disk-And-Drive-Management.md)
