[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# chown (change owner)

`chown` changes which user and group owns a file or directory. It's closely related to `chmod` — permissions decide *what* can be done to a file, `chown` decides *who* those permissions apply to.

Download: [https://man7.org/linux/man-pages/man1/chown.1.html](https://man7.org/linux/man-pages/man1/chown.1.html)

---

## What Is chown?

Every file has an owning user and an owning group. `chown` reassigns either or both — commonly needed after copying files as root, or when handing a project folder over to a different user or service account.

---

## Core Commands

```bash
chown alice file.txt              # Change the owning user
chown alice:developers file.txt   # Change owning user AND group in one command
chown :developers file.txt        # Change only the owning group
chown -R alice:developers folder/ # Apply recursively to a folder and its contents
```

`chown` usually requires `sudo`, since only root (or the current owner, in limited cases) can reassign ownership.

---

## A Common Scenario

```bash
sudo chown -R www-data:www-data /var/www/mysite
```

Web servers like Nginx or Apache often run as a dedicated user (`www-data`). Files uploaded or copied by an admin need their ownership reassigned to that user, or the web server won't be able to read or write them.

---

## Example Walkthrough

```bash
sudo chown -R deploy:deploy /opt/app
sudo chmod -R 750 /opt/app
```

Hands an application folder over to a dedicated `deploy` user and group, then locks down permissions so only that user and group can access it — a typical setup step when deploying a new service.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
