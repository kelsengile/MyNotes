[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# SSH (Secure Shell)

SSH is a protocol and command-line tool for securely logging into and running commands on a remote computer over a network. It encrypts everything sent between the two machines, which is why it replaced older, unencrypted tools like `telnet`.

Download: [https://www.openssh.com/](https://www.openssh.com/)

---

## What Is SSH?

Nearly every remote Linux server — cloud VMs, Raspberry Pis, company servers — is administered over SSH. It gives you a real interactive shell on the remote machine, as if you were sitting in front of it, plus the ability to copy files and forward network ports through the same encrypted connection.

---

## Core Commands

```bash
ssh user@host                    # Connect to a remote machine
ssh -p 2222 user@host            # Connect using a non-default port
ssh -i ~/.ssh/id_ed25519 user@host  # Connect using a specific private key
ssh user@host "ls -la /var/www"  # Run a single command remotely without a full session
scp file.txt user@host:/path/    # Copy a file to a remote machine
scp user@host:/path/file.txt .   # Copy a file from a remote machine
```

---

## Key-Based Authentication

Passwords over SSH work, but key pairs are safer and more convenient. You generate a private/public key pair once, copy the public key to the server, and the server trusts anyone who can prove they hold the matching private key.

```bash
ssh-keygen -t ed25519 -C "you@example.com"   # Generate a new key pair
ssh-copy-id user@host                         # Install your public key on a remote server
```

---

## Example Walkthrough

```bash
ssh-keygen -t ed25519
ssh-copy-id deploy@203.0.113.10
ssh deploy@203.0.113.10 "sudo systemctl restart nginx"
```

Generates a key pair, installs the public key on a server, then uses it to remotely restart a web server — all without ever typing a password.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
