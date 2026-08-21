[Previous](./[27]-Debugging-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[29]-Process-and-Service-Management-Scripts.md)

*System Administration Scripting*

# Lesson 28 - User & Permission Management Scripts

## 28.1 Managing Users on Linux

```bash
sudo useradd -m -s /bin/bash newuser     # create a user with a home directory
sudo passwd newuser                       # set a password
sudo usermod -aG sudo newuser             # add to the sudo group
sudo userdel -r newuser                   # delete a user and their home dir
```

---

## 28.2 File Permissions and Ownership

```bash
chmod 644 file.txt      # rw-r--r--
chmod 755 script.sh     # rwxr-xr-x
chown alice:staff file.txt
```

Permission digits: read=4, write=2, execute=1, summed per owner/group/other (e.g. `755` = owner rwx, group r-x, other r-x).

---

## 28.3 A Bulk User-Creation Script

```bash
#!/bin/bash
set -euo pipefail

while IFS=',' read -r username fullname; do
    sudo useradd -m -c "$fullname" -s /bin/bash "$username"
    echo "Created user: $username"
done < users.csv
```

---

## 28.4 Managing Users on Windows with PowerShell

```powershell
New-LocalUser -Name "newuser" -Password (Read-Host -AsSecureString "Password")
Add-LocalGroupMember -Group "Administrators" -Member "newuser"
Remove-LocalUser -Name "newuser"
```

Scripts that manage users and permissions should always be run with the minimum privilege necessary, and audited carefully — see Lesson 36 for security considerations.

---

[Previous](./[27]-Debugging-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[29]-Process-and-Service-Management-Scripts.md)
