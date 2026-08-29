[Previous](./[9]-Networking-Commands.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B11%5D-User-and-Permissions-Basics.md)

# Lesson 10 - Disk and Drive Management

> Several commands in this lesson can affect data on your disk. Read each explanation before running anything, and always know which drive letter you're targeting.

## 10.1 Checking a disk for errors

```
chkdsk C:
```
Scans drive `C:` and reports (but does not fix) errors it finds.

```
chkdsk C: /f
```
Also **fixes** errors it finds. If the drive is currently in use (which `C:` usually is), Windows will ask to schedule the check for the next restart.

---

## 10.2 Viewing disk space

```
wmic logicaldisk get size,freespace,caption
```
Lists every drive letter with its total and free space in bytes.

For a friendlier view:
```
dir C:\
```
The last line of `dir` output shows bytes free on that drive.

---

## 10.3 Formatting a drive

```
format D:
```
**Erases everything** on drive `D:` and prepares it with a fresh file system. CMD will ask for confirmation before proceeding. Never run this against a drive you're not certain about — there is no undo.

---

## 10.4 Diskpart — advanced partition management

```
diskpart
```
Opens a separate, more powerful tool for partitioning disks. Once inside, common commands include:
- `list disk` — show all physical disks
- `list volume` — show all volumes/partitions
- `select disk 1` — choose a disk to work with
- `exit` — leave diskpart and return to CMD

`diskpart` operates directly on partitions and can destroy data instantly if misused — it's meant for experienced users, and is mentioned here mainly so you recognize it if you encounter it in guides.

---

[Previous](./[9]-Networking-Commands.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B11%5D-User-and-Permissions-Basics.md)
