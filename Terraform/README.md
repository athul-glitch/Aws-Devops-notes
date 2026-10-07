# Terraform Notes

## What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool used to create, manage, and provision infrastructure using configuration files.

## Why Terraform?

- Infrastructure can be managed as code
- Infrastructure changes can be tracked using Git
- Infrastructure can be recreated consistently
- Reduces manual configuration
- Supports multiple cloud providers

## Terraform Workflow

```text
terraform init
      ↓
terraform plan
      ↓
terraform apply
      ↓
terraform destroy
