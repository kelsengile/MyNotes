[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# traceroute

`traceroute` is the Unix/Linux/macOS command for tracing the network path packets take to reach a destination, hop by hop. It serves the same purpose as Windows' `tracert`, with a slightly different default probing method.

Download: [https://man7.org/linux/man-pages/man8/traceroute.8.html](https://man7.org/linux/man-pages/man8/traceroute.8.html)

---

## What Is traceroute?

`traceroute` works by sending packets with a gradually increasing Time To Live (TTL) value. Each router along the path decrements the TTL and, when it hits zero, sends back a "time exceeded" message — which is how `traceroute` builds up the full hop-by-hop path.

---

## Core Commands

```bash
traceroute example.com          # Trace the route to a host
traceroute -n example.com       # Skip reverse DNS lookups (faster, numeric output)
traceroute -m 15 example.com    # Limit the trace to 15 hops
traceroute -I example.com       # Use ICMP echo requests instead of the default UDP probes
```

---

## Reading the Output

```
 1  192.168.1.1 (192.168.1.1)  1.123 ms  1.089 ms  1.045 ms
 2  10.10.0.1 (10.10.0.1)  11.204 ms  10.998 ms  11.301 ms
 3  * * *
 4  203.0.113.1 (203.0.113.1)  33.812 ms  34.001 ms  33.567 ms
```

Just like `tracert`, each line is a hop with three timing samples, and `* * *` means that hop didn't reply — not necessarily a real problem if the trace continues successfully afterward.

---

## Example Walkthrough

```bash
traceroute -n example.com
```

Traces the route with numeric output only, making it quick to scan for the hop where latency suddenly spikes.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
