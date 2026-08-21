[Previous](./[35]-Cross-Platform-Scripting-Considerations.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[37]-Writing-Maintainable-and-Readable-Scripts.md)

*Best Practices*

# Lesson 36 - Script Security Best Practices

## 36.1 Never Hardcode Secrets

Hardcoded API keys, passwords, or tokens in a script are a serious security risk, especially once the script is committed to version control. Use environment variables or a secrets manager instead (Lesson 15).

---

## 36.2 Validate and Sanitize Input

Untrusted input (arguments, files, API responses) should never be passed directly into a shell command:

```bash
# Dangerous: allows command injection if $filename is attacker-controlled
eval "cat $filename"

# Safer: pass as an argument, don't build a command string
cat -- "$filename"
```

In Python, avoid `os.system()` or `shell=True` with untrusted input; prefer `subprocess.run([...])` with a list of arguments.

---

## 36.3 Principle of Least Privilege

Run scripts with the minimum permissions they need. Avoid `sudo` or Administrator rights unless a specific step genuinely requires them, and scope those elevated sections as narrowly as possible.

---

## 36.4 PowerShell Execution Policy

PowerShell blocks unsigned scripts by default. Understand what you're changing before adjusting it:

```powershell
Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

`RemoteSigned` (requires downloaded scripts to be signed) is a safer default than `Unrestricted`.

---

## 36.5 Review Third-Party Scripts Before Running

Never pipe a downloaded script straight into an interpreter without reading it first:

```bash
curl -s https://example.com/install.sh | bash   # risky — inspect before running
```

---

[Previous](./[35]-Cross-Platform-Scripting-Considerations.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[37]-Writing-Maintainable-and-Readable-Scripts.md)
