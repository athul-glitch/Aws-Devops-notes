# AWS CloudFormation

## What is AWS CloudFormation?

AWS CloudFormation is an **Infrastructure as Code (IaC)** service that allows you to define and provision AWS resources using configuration files called **templates**.

Instead of manually creating resources through the AWS Console, you describe the infrastructure in a template.

Example:

```text
CloudFormation Template
        |
        ↓
CloudFormation Stack
        |
 ┌──────┼────────┐
 ↓      ↓        ↓
VPC    EC2       S3
```

CloudFormation then creates and manages those resources.

---

## Why Use CloudFormation?

Manual infrastructure creation can become difficult when an environment contains many resources.

For example:

```text
VPC
├── Subnets
├── Route Tables
├── Internet Gateway
├── Security Groups
├── EC2
├── IAM Roles
└── S3
```

CloudFormation allows this infrastructure to be defined as code.

Benefits include:

* Repeatable infrastructure
* Consistent environments
* Version-controlled infrastructure
* Automated resource creation
* Easier infrastructure management
* Easier deletion of complete environments

---

## CloudFormation Template

A CloudFormation template describes the desired AWS infrastructure.

Templates are commonly written in:

* YAML
* JSON

YAML is commonly easier to read.

Basic example:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Resources:

  MyBucket:
    Type: AWS::S3::Bucket
```

This defines an S3 bucket.

---

## CloudFormation Stack

A **stack** is the collection of AWS resources created and managed from a CloudFormation template.

Example:

```text
Template
   |
   ↓
Stack
   |
   ├── VPC
   ├── Subnet
   ├── Security Group
   └── EC2
```

CloudFormation manages these resources as a group.

If the stack is deleted, CloudFormation can delete the resources associated with it according to their configured deletion behavior.

---

## Resources

The `Resources` section defines the AWS resources that CloudFormation should create.

Example:

```yaml
Resources:

  MyBucket:
    Type: AWS::S3::Bucket
```

Important parts:

```text
Logical ID
    ↓
MyBucket

Resource Type
    ↓
AWS::S3::Bucket
```

The logical ID is the identifier used for the resource inside the template.

---

## Parameters

Parameters allow users to provide values when creating or updating a stack.

Example:

```yaml
Parameters:

  InstanceType:
    Type: String
    Default: t3.micro
```

The same template can then be used with different instance types.

For example:

```text
Development → t3.micro
Production  → t3.small
```

This makes templates more reusable.

---

## Outputs

Outputs expose useful information from a CloudFormation stack.

Example:

```yaml
Outputs:

  BucketName:
    Value: !Ref MyBucket
```

Outputs can be useful for displaying or passing information such as:

* Resource IDs
* DNS names
* Load Balancer endpoints
* S3 bucket names

---

## Mappings

Mappings allow predefined values to be associated with specific keys.

They can be useful when values depend on factors such as:

* Region
* Environment
* Architecture

Conceptually:

```text
Region
  |
  ├── ap-south-1 → value A
  ├── us-east-1  → value B
  └── eu-west-1  → value C
```

Mappings are less commonly needed in simple templates but are useful in more advanced templates.

---

## Conditions

Conditions allow resources or properties to be created only when a specific condition is true.

Example concept:

```text
Environment = production
        |
        ↓
Create production-only resource
```

This can help create different infrastructure depending on the environment.

---

## Intrinsic Functions

CloudFormation provides intrinsic functions for dynamically working with template values.

Common examples include:

* `Ref`
* `Fn::GetAtt`
* `Fn::Sub`
* `Fn::Join`
* `Fn::Select`
* `Fn::If`

Example:

```yaml
Value: !Ref MyBucket
```

`Ref` can return a resource's reference value.

Another common function is:

```yaml
!GetAtt
```

which can retrieve an attribute of a resource.

---

## Dependencies

CloudFormation automatically determines many resource dependencies.

Example:

```text
VPC
 ↓
Subnet
 ↓
EC2
```

CloudFormation understands that resources may need to be created in a particular order.

You can also explicitly define a dependency using `DependsOn` when necessary.

---

## CloudFormation Change Sets

A **Change Set** allows you to preview how a stack would change before actually applying the changes.

Example:

```text
Existing Stack
      |
      ↓
Change Set
      |
      ↓
Review Changes
      |
      ↓
Execute
```

This is useful when modifying production infrastructure.

---

## Stack Updates

CloudFormation can update an existing stack when the template changes.

Example:

```text
Old Template
     |
     ↓
Modify Template
     |
     ↓
Update Stack
     |
     ↓
AWS Resources Updated
```

CloudFormation determines which resources need to be changed.

Some changes can occur without replacing a resource, while other changes may require resource replacement.

---

## Resource Replacement

Some infrastructure changes require CloudFormation to replace an existing resource.

For example:

```text
Old Resource
     |
     ↓
Replacement Required
     |
     ↓
New Resource
```

This is important when working with production infrastructure because replacement can potentially cause downtime or data loss depending on the resource and configuration.

Always review the proposed changes before applying them.

---

## Stack Deletion

CloudFormation can delete the resources managed by a stack.

Example:

```text
CloudFormation Stack
       |
       ↓
Delete Stack
       |
 ┌─────┼─────┐
 ↓     ↓     ↓
EC2   SG    S3
```

Resource deletion behavior can be controlled in certain situations using deletion policies.

For important resources such as databases, you should carefully consider whether they should be deleted with the stack.

---

## DeletionPolicy

`DeletionPolicy` controls what happens to certain resources when they are removed from a stack or when the stack is deleted.

Common options include:

* `Delete`
* `Retain`
* `Snapshot`

For example, a database may be configured to retain or snapshot data rather than being immediately deleted.

This is especially important for production databases.

---

## CloudFormation Drift

**Drift** occurs when the actual AWS resource configuration differs from the configuration defined in the CloudFormation template.

Example:

```text
CloudFormation Template
        |
        | Security Group allows port 80
        ↓

Actual AWS Resource
        |
        | Someone manually changes it to port 8080
        ↓

Configuration ≠ Template
```

CloudFormation can detect this difference through **drift detection**.

This is one reason IaC is useful: the template remains the defined source of infrastructure configuration.

---

## Nested Stacks

A large CloudFormation deployment can be divided into smaller templates.

These can be organized using nested stacks.

Example:

```text
Main Stack
   |
   ├── Networking Stack
   ├── Security Stack
   └── Application Stack
```

This can make large CloudFormation projects easier to organize.

---

## CloudFormation vs Terraform

Both CloudFormation and Terraform are Infrastructure as Code tools.

| Feature          | CloudFormation          | Terraform         |
| ---------------- | ----------------------- | ----------------- |
| Provider         | AWS                     | Multi-cloud       |
| AWS integration  | Native                  | Via AWS provider  |
| Template         | YAML/JSON               | HCL               |
| State            | AWS manages stack state | Terraform state   |
| AWS resources    | Excellent support       | Excellent support |
| Multi-cloud      | Limited                 | Strong            |
| AWS-specific IaC | Very suitable           | Very suitable     |

### Simple difference

```text
CloudFormation
      ↓
AWS-focused IaC

Terraform
      ↓
Multi-cloud IaC
```

For your learning path, **Terraform is more important to practice deeply** because you are already using it in Sheepeye.

You should still understand CloudFormation because it is an important AWS service and can appear in AWS architecture/SAA scenarios.

---

## CloudFormation vs Manual Console

### Manual

```text
AWS Console
   ↓
Create VPC
   ↓
Create Subnet
   ↓
Create Security Group
   ↓
Create EC2
```

### CloudFormation

```text
Template
   ↓
CloudFormation
   ↓
Infrastructure
```

IaC makes infrastructure easier to reproduce and version-control.

---

## CloudFormation and AWS Services

CloudFormation can be used to provision many AWS resources, including:

* VPC
* Subnets
* Route Tables
* Internet Gateway
* Security Groups
* EC2
* S3
* IAM
* RDS
* Lambda
* ALB
* Auto Scaling
* CloudWatch
* SNS
* SQS
* Route 53

This makes it useful for building complete AWS environments.

---

## CloudFormation and Sheepeye

Your Sheepeye project currently uses Terraform for infrastructure.

Conceptually, the same infrastructure could also be created using CloudFormation:

```text
CloudFormation
      |
      ↓
VPC
 |
 ├── Subnet
 ├── Route Table
 ├── Internet Gateway
 ├── Security Group
 └── EC2
```

However, **you do not need to convert Sheepeye from Terraform to CloudFormation**.

Keep Terraform as your primary IaC implementation and learn CloudFormation as an AWS concept.

---

## CloudFormation in SAA Scenarios

CloudFormation is useful when a question describes:

* Repeatable AWS infrastructure
* Automated infrastructure deployment
* Infrastructure as Code
* Consistent environments
* Recreating infrastructure
* Version-controlled infrastructure
* AWS-native infrastructure automation

Example scenario:

> A company wants to deploy identical AWS environments for development, testing, and production using infrastructure as code.

CloudFormation or Terraform would be appropriate solutions.

If the question specifically asks for an **AWS-native IaC service**, CloudFormation is the obvious choice.

---

## Key Points

* CloudFormation is AWS's Infrastructure as Code service.
* Infrastructure is defined using YAML or JSON templates.
* A CloudFormation stack represents the resources managed from a template.
* `Resources` defines AWS resources.
* `Parameters` make templates reusable.
* `Outputs` expose useful stack information.
* `Conditions` control conditional resource creation.
* Intrinsic functions dynamically reference values.
* Change Sets allow you to review changes before applying them.
* Drift detection identifies differences between the template and actual infrastructure.
* `DeletionPolicy` can help protect important resources.
* CloudFormation is AWS-focused, while Terraform is multi-cloud.
* CloudFormation can automate the creation and management of complete AWS environments.
* For your Sheepeye project, continue using **Terraform** rather than adding CloudFormation unnecessarily.
