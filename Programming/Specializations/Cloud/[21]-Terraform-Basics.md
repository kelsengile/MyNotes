[Previous](./[20]-Introduction-to-Infrastructure-as-Code.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[22]-Cloud-Native-IaC-Tools.md)

*Infrastructure as Code*

# Lesson 21 - Terraform Basics

## 21.1 Terraform Concepts

**Terraform** (by HashiCorp) is a declarative, multi-cloud IaC tool that uses its own configuration language, **HCL (HashiCorp Configuration Language)**, to define infrastructure. Because it supports "providers" for virtually every major cloud and SaaS platform, a single Terraform workflow can manage resources across AWS, Azure, GCP, and beyond — a major advantage over cloud-specific tools like CloudFormation.

---

## 21.2 Providers, Resources, State

A basic Terraform configuration has three key pieces:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.micro"
  tags = {
    Name = "web-server"
  }
}
```

- **Provider** — declares which cloud/platform Terraform should talk to and how to authenticate.
- **Resource** — declares a piece of infrastructure to create (a VM, a bucket, a database).
- **State** — Terraform keeps a state file (`terraform.tfstate`) recording what infrastructure it has actually created, so it can compare your configuration against reality and calculate exactly what needs to change. In team settings, state is typically stored remotely (e.g. in an S3 bucket) rather than on a single developer's machine, so everyone works from the same source of truth.

---

## 21.3 Basic Workflow (init/plan/apply)

The standard Terraform workflow follows three commands:

```bash
terraform init    # downloads providers, sets up the working directory
terraform plan    # shows what changes would be made, without applying them
terraform apply   # actually creates/updates/deletes resources to match config
```

`terraform plan` is a critical safety step — it lets you review exactly what will be added, changed, or destroyed before committing to it. To tear down everything Terraform manages, `terraform destroy` removes all resources defined in the configuration.

---

[Previous](./[20]-Introduction-to-Infrastructure-as-Code.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[22]-Cloud-Native-IaC-Tools.md)
