# Lesson 11: User and Permissions Basics

## Checking your own permissions

```
whoami /priv
```
Lists the special privileges your current login has (like shutting down the system or debugging programs).

```
whoami /groups
```
Lists which security groups you belong to (for example, "Administrators" or "Users").

## Running CMD as Administrator

Some commands (creating user accounts, changing certain system settings, some `diskpart` and `netsh` operations) require elevated permissions.

To open an elevated CMD window:
1. Search "Command Prompt" in the Start menu.
2. Right-click it and choose **Run as administrator**.
3. Approve the User Account Control (UAC) prompt.

You'll know it worked because the window title bar says "Administrator: Command Prompt."

## Listing user accounts on this machine

```
net user
```
Shows all local user accounts.

```
net user YourName
```
Shows details about one specific account (full name, group membership, password settings).

## Creating a local user account (requires Administrator)

```
net user NewUser Password123 /add
```
Creates a new local account called `NewUser` with the given password.

## Adding a user to the Administrators group (requires Administrator)

```
net localgroup Administrators NewUser /add
```

## Removing a user account (requires Administrator)

```
net user NewUser /delete
```

## A note on safety

Account and permission commands can lock you out of your own system or grant unintended access if used carelessly. Test on a machine you're comfortable experimenting with, and double-check account names before adding or deleting anything.

## Try it yourself

1. Run `whoami /groups` and see what groups you belong to.
2. Run `net user` and see the list of accounts on your machine.
3. Run `net user <YourAccountName>` to see your own account's details.

