# Terraform Notes

Practical Terraform notes focused on Infrastructure as Code (IaC), AWS infrastructure, cloud infrastructure automation, and Terraform workflows.

---

## What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool used to create, manage, and provision infrastructure using configuration files.

Terraform allows infrastructure to be defined as code instead of creating resources manually through a cloud provider's console.

Terraform is commonly used with:

* AWS
* Azure
* Google Cloud
* Kubernetes
* Other infrastructure platforms

---

## Why Terraform?

Terraform helps to:

* Manage infrastructure as code
* Track infrastructure changes using Git
* Recreate infrastructure consistently
* Reduce manual configuration
* Automate infrastructure deployment
* Review infrastructure changes before applying them
* Manage infrastructure across multiple environments
* Support multiple cloud providers

---

## Infrastructure as Code

Instead of manually creating infrastructure:

```text
AWS Console
    |
    +-- Create VPC
    +-- Create Subnet
    +-- Create EC2
    +-- Configure Security Group
```

Terraform allows the infrastructure to be defined in code:

```text
Terraform Configuration
        |
        v
Terraform
        |
        v
AWS Infrastructure
```

---

## Terraform Configuration Files

Terraform configuration files normally use the `.tf` extension.

Common files include:

```text
main.tf
variables.tf
outputs.tf
providers.tf
terraform.tf
```

A small Terraform project can look like:

```text
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
└── terraform.tfvars
```

Terraform automatically loads `.tf` files in the working directory.

---

## Terraform Provider

A provider allows Terraform to interact with an infrastructure platform or service.

For AWS:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}
```

The AWS provider allows Terraform to create and manage AWS resources.

---

## Terraform Resource

A resource represents an infrastructure object that Terraform manages.

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "main-vpc"
  }
}
```

Here:

* `aws_vpc` is the resource type
* `main` is the local resource name
* `cidr_block` defines the VPC network range

---

## Terraform Variables

Variables allow configuration values to be reused instead of hardcoding them.

Example:

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "ap-south-1"
}
```

The variable can be used with:

```hcl
provider "aws" {
  region = var.aws_region
}
```

---

## Terraform Outputs

Outputs display useful information after Terraform creates infrastructure.

Example:

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

After applying the configuration, Terraform can display the VPC ID.

---

## Terraform State

Terraform uses a state file to keep track of infrastructure managed by Terraform.

The default state file is:

```text
terraform.tfstate
```

Terraform uses state to understand:

* What resources it manages
* Current resource information
* Relationships between resources
* What changes need to be made

### Important

Do not normally commit `terraform.tfstate` to a public GitHub repository because it can contain sensitive infrastructure information.

Add it to `.gitignore`:

```text
terraform.tfstate
terraform.tfstate.*
```

---

## Terraform Init

`terraform init` initializes a Terraform working directory.

It downloads required providers and prepares the working directory.

Command:

```bash
terraform init
```

Example output includes initialization of the AWS provider.

---

## Terraform Validate

`terraform validate` checks whether the Terraform configuration is syntactically valid and internally consistent.

Command:

```bash
terraform validate
```

---

## Terraform Format

`terraform fmt` formats Terraform configuration files into the standard Terraform style.

Command:

```bash
terraform fmt
```

To format all Terraform files:

```bash
terraform fmt -recursive
```

---

## Terraform Plan

`terraform plan` shows the changes Terraform intends to make.

Command:

```bash
terraform plan
```

Example:

```text
+ create
~ update
- destroy
```

The plan allows you to review changes before applying them.

---

## Terraform Apply

`terraform apply` creates or modifies infrastructure according to the Terraform configuration.

Command:

```bash
terraform apply
```

Terraform normally asks for confirmation before making changes.

To automatically approve:

```bash
terraform apply -auto-approve
```

Use `-auto-approve` carefully, especially with production infrastructure.

---

## Terraform Destroy

`terraform destroy` removes infrastructure managed by the Terraform configuration.

Command:

```bash
terraform destroy
```

Automatic approval:

```bash
terraform destroy -auto-approve
```

Be careful with this command because it can delete real infrastructure.

---

## Terraform Workflow

The common Terraform workflow is:

```text
Write Configuration
        |
        v
terraform fmt
        |
        v
terraform init
        |
        v
terraform validate
        |
        v
terraform plan
        |
        v
terraform apply
        |
        v
Infrastructure Created
```

When infrastructure is no longer required:

```text
terraform destroy
```

---

## Terraform Dependencies

Terraform automatically understands dependencies between resources.

Example:

```text
VPC
 |
 +-- Subnet
      |
      +-- EC2
```

If an EC2 instance depends on a subnet, Terraform can determine the correct creation order.

Explicit dependencies can also be defined using `depends_on`.

Example:

```hcl
depends_on = [
  aws_internet_gateway.main
]
```

---

## Terraform Data Sources

Data sources allow Terraform to retrieve information about existing infrastructure.

Example:

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}
```

Data sources are useful when Terraform needs information about resources that it does not manage.

---

## Terraform Modules

A module is a reusable collection of Terraform configuration files.

Example:

```text
modules/
├── vpc/
├── ec2/
└── security-group/
```

Modules help avoid repeating the same infrastructure configuration.

Example:

```text
Root Module
    |
    +-- VPC Module
    |
    +-- EC2 Module
    |
    +-- Security Group Module
```

---

## Terraform Backend

A backend determines where Terraform stores its state.

The default backend stores state locally.

For team environments, remote state is commonly used.

AWS environments may use:

* Amazon S3 for state storage
* Locking mechanism appropriate to the Terraform/backend setup

Remote state helps teams work with shared infrastructure safely.

---

## Terraform State Commands

Useful commands include:

```bash
terraform state list
```

Lists resources tracked in the state.

```bash
terraform state show RESOURCE
```

Shows information about a resource in the state.

```bash
terraform show
```

Displays the current state or a saved plan.

---

## Terraform Import

Terraform import allows an existing infrastructure resource to be brought under Terraform management.

Example:

```bash
terraform import aws_vpc.main vpc-xxxxxxxx
```

The exact resource address and ID depend on the resource being imported.

---

## Terraform Refresh

Terraform compares the real infrastructure with the information stored in state during normal planning and operations.

A plan can be used to detect differences between configuration, state, and real infrastructure.

---

## Terraform Variables and Environments

Variables can be used to manage different environments.

Example:

```text
environments/
├── dev/
├── staging/
└── production/
```

Different values can be supplied for:

* AWS region
* Instance type
* CIDR ranges
* Instance count
* Environment name

---

## Terraform with AWS

A typical AWS infrastructure project can use Terraform to create:

```text
VPC
 |
 +-- Internet Gateway
 |
 +-- Public Subnet
 |
 +-- Private Subnet
 |
 +-- Route Tables
 |
 +-- Security Groups
 |
 +-- EC2
 |
 +-- RDS
 |
 +-- S3
```

Terraform can manage these resources from configuration files.

---

## Terraform and Git

Terraform configuration should normally be stored in Git.

Example workflow:

```text
Terraform Code
      |
      v
Git
      |
      v
GitHub
```

This provides:

* Version history
* Change tracking
* Collaboration
* Review of infrastructure changes
* Ability to revert code changes

---

## Terraform `.gitignore`

A Terraform project should normally exclude files such as:

```text
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
crash.log
crash.*.log
```

Be careful with `.tfvars` files because they may contain sensitive values.

---

## Terraform Best Practices

* Use meaningful resource names.
* Use variables instead of unnecessary hardcoded values.
* Use outputs for important resource information.
* Keep Terraform code formatted.
* Review `terraform plan` before applying changes.
* Store Terraform code in Git.
* Do not commit Terraform state files.
* Do not hardcode AWS access keys or secrets.
* Use IAM roles and secure credential mechanisms.
* Use modules when infrastructure becomes repetitive.
* Separate development and production environments.
* Avoid running `terraform destroy` against production infrastructure without verifying the target.
* Use remote state when working in a team.

---

## Basic Terraform Project Example

A simple AWS VPC project:

```text
sheepeye2/
└── Terraform/
    ├── provider.tf
    ├── vpc.tf
    ├── subnet.tf
    ├── variables.tf
    ├── outputs.tf
    └── .gitignore
```

Example workflow:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

When the infrastructure is no longer required:

```bash
terraform destroy
```

---

## Terraform Workflow Summary

```text
                    Terraform Configuration
                              |
                              v
                       terraform init
                              |
                              v
                      terraform validate
                              |
                              v
                         terraform plan
                              |
                              v
                        terraform apply
                              |
                              v
                      AWS Infrastructure
                              |
                              v
                       Terraform State
```

---

## Key Takeaways

* Terraform is an Infrastructure as Code tool.
* Terraform configuration is written mainly in HCL.
* Providers allow Terraform to interact with platforms such as AWS.
* Resources represent infrastructure managed by Terraform.
* Variables make configurations reusable.
* Outputs expose useful resource information.
* State allows Terraform to track managed infrastructure.
* `terraform init` initializes a project.
* `terraform fmt` formats configuration.
* `terraform validate` checks configuration.
* `terraform plan` previews changes.
* `terraform apply` applies changes.
* `terraform destroy` removes managed infrastructure.
* Modules allow infrastructure code to be reused.
* Data sources retrieve information about existing infrastructure.
* Git provides version control for Terraform code.
* Secrets and state files should not be exposed in public repositories.
