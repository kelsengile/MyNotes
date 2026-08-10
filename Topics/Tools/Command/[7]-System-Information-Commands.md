# Lesson 7: System Information Commands

## Who am I logged in as?

```
whoami
```
Shows your username in `DOMAIN\username` or `COMPUTERNAME\username` format.

```
whoami /all
```
Shows your username plus group memberships and permissions — useful for troubleshooting access issues.

## Machine name

```
hostname
```
Prints just the computer's network name.

## Windows version

```
ver
```
A quick one-liner with the Windows version and build number.

## Full system report

```
systeminfo
```
A detailed dump: OS version, install date, memory, boot time, network cards, hotfixes installed, and more. Takes a few seconds to run.

To narrow it down, pipe it through `find`:
```
systeminfo | find "Total Physical Memory"
```

## Listing installed hotfixes

```
wmic qfe list
```
Shows installed Windows updates — handy when checking if a security patch is present.

## Checking disk space

```
wmic logicaldisk get size,freespace,caption
```
Shows total size and free space for every drive letter.

## Try it yourself

1. Run `whoami` and then `whoami /all` — compare the amount of detail.
2. Run `systeminfo` and find your Windows version and total memory.
3. Run `systeminfo | find "OS Name"` to pull out just that one line.
