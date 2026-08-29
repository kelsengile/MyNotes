[Previous](./[21]-Terraform-Basics.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[23]-Docker-Fundamentals.md)

*Infrastructure as Code*

# Lesson 22 - Cloud-Native IaC Tools (CloudFormation, ARM/Bicep)

## 22.1 AWS CloudFormation

**CloudFormation** is AWS's native IaC service, defining resources in YAML or JSON "templates." It's deeply integrated with AWS — supporting every AWS service on day one, and offering features like **stacks** (a group of resources managed together as a single unit) and **change sets** (a preview of what a template update will do, similar to `terraform plan`). Because it's AWS-only, it's a strong choice for teams fully committed to AWS who want the tightest possible integration, but it doesn't extend to other clouds.

```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-app-assets
```

---

## 22.2 Azure ARM Templates and Bicep

**ARM (Azure Resource Manager) templates** are Azure's native IaC format, written in JSON, describing the resources to deploy to a resource group. Because raw ARM JSON is verbose and hard to hand-write, Microsoft created **Bicep**, a cleaner domain-specific language that compiles down to ARM JSON automatically:

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: 'mystorageacct'
  location: 'eastus'
  sku: { name: 'Standard_LRS' }
  kind: 'StorageV2'
}
```

Bicep has largely replaced hand-written ARM templates for new Azure IaC work due to its simpler syntax.

---

## 22.3 GCP Deployment Manager / Native Tools

Google Cloud's native IaC option, **Deployment Manager**, uses YAML templates (optionally extended with Python or Jinja2 for logic) to define resources. In practice, however, Terraform has become the dominant choice for managing GCP infrastructure, even more so than on AWS or Azure, since GCP never pushed a native tool as heavily as its competitors. Choosing between a cloud-native tool and Terraform generally comes down to: native tools offer day-one support for every new service and the tightest platform integration, while Terraform offers one consistent workflow and language across multiple providers — valuable for any team using more than one cloud.

---

[Previous](./[21]-Terraform-Basics.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[23]-Docker-Fundamentals.md)
