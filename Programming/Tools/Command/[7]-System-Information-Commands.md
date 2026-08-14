[Previous](./[6]-Environment-Variables-And-PATH.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[8]-Process-And-Task-Management.md)

# Lesson 7 - System Information Commands

## 7.1 Who am I logged in as?

```
whoami
```
Shows your username in `DOMAIN\username` or `COMPUTERNAME\username` format.

```
whoami /all
```
Shows your username plus group memberships and permissions — useful for troubleshooting access issues.

---

## 7.2 Machine name

```
hostname
```
Prints just the computer's network name.

---

## 7.3 Windows version

```
ver
```
A quick one-liner with the Windows version and build number.

---

## 7.4 Full system report

```
systeminfo
```
A detailed dump: OS version, install date, memory, boot time, network cards, hotfixes installed, and more. Takes a few seconds to run.

To narrow it down, pipe it through `find`:
```
systeminfo | find "Total Physical Memory"
```

---

## 7.5 Listing installed hotfixes

```
wmic qfe list
```
Shows installed Windows updates — handy when checking if a security patch is present.

---

## 7.6 Checking disk space

```
wmic logicaldisk get size,freespace,caption
```
Shows total size and free space for every drive letter.

---

[Previous](./[6]-Environment-Variables-And-PATH.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[8]-Process-And-Task-Management.md)
