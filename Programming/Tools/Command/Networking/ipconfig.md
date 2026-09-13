[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# ipconfig

`ipconfig` is the Windows command for viewing and managing your network interface configuration — IP address, subnet mask, default gateway, and DNS settings.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/ipconfig](https://learn.microsoft.com/windows-server/administration/windows-commands/ipconfig)

---

## What Is ipconfig?

Every network adapter on a Windows machine (Wi-Fi, Ethernet, VPN) has its own configuration. `ipconfig` prints that configuration per adapter, which is the starting point for diagnosing "why can't I connect to anything" or "why can't others connect to me."

This tool is also covered as part of [Lesson 9 - Networking Commands](../[9]-Networking-Commands.md) in the main lesson series.

---

## Core Commands

```cmd
ipconfig                 # Show basic info: IP, subnet mask, gateway, per adapter
ipconfig /all             # Show full details: MAC address, DNS servers, DHCP info
ipconfig /release         # Release the current DHCP-assigned IP address
ipconfig /renew           # Request a new IP address from DHCP
ipconfig /flushdns        # Clear the local DNS resolver cache
```

---

## A Classic Fix

"My Wi-Fi is connected but nothing loads" is often solved by releasing and renewing the IP address, which forces the machine to get a fresh configuration from the router:

```cmd
ipconfig /release
ipconfig /renew
```

---

## Example Walkthrough

```cmd
ipconfig /all
ipconfig /flushdns
ipconfig /renew
```

Reviews the full network configuration to spot anything unusual, clears any stale DNS entries, then requests a fresh IP address — a common troubleshooting sequence for flaky connections.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
