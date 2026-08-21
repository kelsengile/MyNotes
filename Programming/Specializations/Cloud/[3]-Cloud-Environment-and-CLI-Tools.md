[Previous](./[2]-Choosing-a-Cloud-Provider.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[4]-Regions-Availability-Zones-and-Global-Infrastructure.md)

*Getting Started*

# Lesson 3 - Setting Up Your Cloud Environment & CLI Tools

## 3.1 Creating a Cloud Account

To follow along with cloud tutorials hands-on, you'll need an account with a provider (AWS, Azure, or GCP all offer free tiers). Sign-up requires an email, payment method (for identity verification, even on the free tier), and phone verification. Immediately after creating an account, it's good practice to set a billing alert/budget so you're notified if usage unexpectedly incurs cost, and to enable multi-factor authentication (MFA) on the root/owner account, since that account has unrestricted access.

---

## 3.2 Installing and Configuring the CLI

Every major provider ships a command-line interface (CLI) that lets you manage resources from a terminal instead of clicking through a web console — essential for scripting and automation:

- AWS: `aws` CLI, installed via package manager or installer, configured with `aws configure`.
- Azure: `az` CLI, configured with `az login`.
- GCP: `gcloud` CLI, configured with `gcloud init`.

A typical AWS setup looks like:

```bash
aws configure
# AWS Access Key ID: ****************
# AWS Secret Access Key: ****************
# Default region name: us-east-1
# Default output format: json
```

Once configured, you can run commands like `aws s3 ls` or `aws ec2 describe-instances` to interact with resources directly.

---

## 3.3 Authentication and Credentials

The CLI authenticates using credentials, most commonly an **access key ID** and **secret access key** (AWS), a **service principal** (Azure), or a **service account key** (GCP). These credentials should never be committed to source control or shared — they behave like a username and password to your cloud account. Best practices include:

- Never using root/owner account credentials for everyday CLI work — create a limited IAM user or role instead (covered in Lesson 5).
- Storing credentials in environment variables or a credentials file outside your project folder, not hardcoded in scripts.
- Rotating access keys periodically and revoking unused ones.
- Using short-lived, temporary credentials (via IAM roles) wherever possible instead of long-lived keys.

---

[Previous](./[2]-Choosing-a-Cloud-Provider.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[4]-Regions-Availability-Zones-and-Global-Infrastructure.md)
