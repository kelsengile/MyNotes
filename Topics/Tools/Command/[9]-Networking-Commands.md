# Lesson 9: Networking Commands

## Viewing your network configuration

```
ipconfig
```
Shows your IP address, subnet mask, and default gateway for each network adapter.

```
ipconfig /all
```
Adds MAC addresses, DNS servers, DHCP details, and more.

## Renewing your IP address

If your internet connection is misbehaving, these two in sequence are a classic fix:
```
ipconfig /release
ipconfig /renew
```

## Flushing the DNS cache

```
ipconfig /flushdns
```
Clears cached DNS lookups — useful if a website recently changed servers and your computer is still using an old address.

## Testing if a host is reachable

```
ping google.com
```
Sends a handful of test packets and reports whether they came back, and how long it took. Great first step when troubleshooting "is the internet down?"

```
ping -t google.com
```
Pings continuously until you stop it with `Ctrl + C` — handy for watching a connection over time.

## Tracing the path to a host

```
tracert google.com
```
Shows every network hop (router) between you and the destination, with timing for each — useful for spotting *where* a connection is slow or failing.

## Viewing active connections

```
netstat
```
Lists active network connections.

```
netstat -an
```
Shows all connections and listening ports, with addresses/ports shown as numbers rather than resolved names (faster, and useful for checking what's listening on your machine).

## Looking up a domain's IP address

```
nslookup google.com
```
Shows the IP address(es) a domain name resolves to.

## Try it yourself

1. Run `ipconfig` and identify your IP address.
2. Run `ping google.com` and check your latency.
3. Run `tracert google.com` and count how many hops it takes.
4. Run `nslookup google.com` and compare the IP to what `ping` showed.

