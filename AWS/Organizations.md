# AWS Organizations

## What is AWS Organizations?

AWS Organizations is an AWS service used to **centrally manage multiple AWS accounts**.

Instead of managing every AWS account separately, an organization can group and control accounts from a central management account.

Example:

```text
AWS Organization
       |
       ├── Management Account
       |
       ├── Development Account
       ├── Production Account
       ├── Testing Account
       └── Security Account
```

This is useful for companies that separate workloads into multiple AWS accounts.

---

## Why Use Multiple AWS Accounts?

Large organizations often avoid putting everything into one AWS account.

Separate accounts can provide:

* Better security isolation
* Separate billing
* Easier access control
* Environment isolation
* Reduced blast radius
* Different security requirements

Example:

```text
Development
    ↓
AWS Account 1

Production
    ↓
AWS Account 2
```

If something goes wrong in the development account, production resources are isolated from it.

---

## AWS Organization Structure

An AWS Organization can contain:

* Management account
* Member accounts
* Organizational Units (OUs)
* Service Control Policies (SCPs)

Example:

```text
Organization
│
├── Management Account
│
├── Production OU
│   ├── Production Account
│   └── Database Account
│
├── Development OU
│   └── Development Account
│
└── Security OU
    └── Security Account
```

---

## Management Account

The **management account** is the central account for the AWS Organization.

It can:

* Create member accounts
* Manage organizational policies
* Manage billing relationships
* Organize accounts into OUs
* Enable organization-wide services

The management account should be protected carefully and should not be used for normal application workloads.

---

## Member Accounts

Member accounts are AWS accounts that belong to the organization.

For example:

```text
Organization
   |
   ├── Management Account
   ├── Dev Account
   ├── Test Account
   └── Production Account
```

Each member account can have its own:

* EC2 instances
* S3 buckets
* VPCs
* IAM users/roles
* Databases
* Applications

Organizations provides centralized management across these accounts.

---

## Organizational Units (OUs)

An **Organizational Unit (OU)** is a logical grouping of AWS accounts.

Example:

```text
Organization
   |
   ├── Production OU
   │      ├── Account A
   │      └── Account B
   │
   └── Development OU
          ├── Account C
          └── Account D
```

OUs make it easier to apply policies to multiple accounts.

---

## Service Control Policies (SCPs)

A **Service Control Policy (SCP)** is an organization-level policy that defines the maximum permissions available to accounts within the organization.

An important point:

> SCPs do not grant permissions.

They act as a permission boundary at the organization level.

Example:

```text
SCP
 |
 ↓
Production OU
 |
 ├── Account A
 └── Account B
```

The SCP can restrict what actions those accounts are allowed to perform.

---

## SCP Example

Suppose an organization does not want users in a development account to use certain AWS regions.

An SCP can restrict access to those regions.

Conceptually:

```text
Development Account
       |
       ↓
SCP
       |
       ↓
Allowed Regions
       |
       ├── ap-south-1 ✓
       ├── us-east-1  ✓
       └── Other Regions ✗
```

Even if an IAM user has an identity policy allowing an action, an SCP can prevent that action at the organization level.

---

## SCP vs IAM Policy

This distinction is important for AWS exams.

### IAM Policy

Controls what a principal can do.

```text
IAM Policy
    ↓
User / Role
    ↓
Permissions
```

### SCP

Controls the maximum permissions available to accounts in the organization.

```text
SCP
 ↓
AWS Account / OU
 ↓
Maximum allowed permissions
```

An SCP does **not** provide permission by itself.

For an action to succeed, the action must be allowed by the applicable IAM policies and not blocked by an SCP or another explicit deny.

---

## Explicit Deny

An explicit deny takes precedence over an allow.

Example:

```text
IAM Policy
    ↓
Allow S3 DeleteObject
```

But:

```text
SCP
    ↓
Deny S3 DeleteObject
```

Result:

```text
❌ DeleteObject denied
```

This is an important AWS permission concept.

---

## AWS Organizations and Billing

Organizations can provide centralized billing across member accounts.

Example:

```text
Management Account
        |
        ↓
Consolidated Billing
        |
 ┌──────┼────────┐
 ↓      ↓        ↓
Dev    Test     Prod
```

The organization can receive one consolidated bill while still keeping workloads separated into different accounts.

---

## Cost Benefits

Consolidated billing can also allow eligible usage across accounts to contribute toward certain AWS pricing benefits.

However, account separation should primarily be designed around **security, governance, and operational requirements**, not simply billing.

---

## AWS Account Creation

Organizations can create or invite AWS accounts into the organization.

Typical structure:

```text
Management Account
       |
       ↓
AWS Organizations
       |
       ├── Create Account
       └── Invite Existing Account
```

New accounts can then be placed into appropriate OUs and governed by organizational policies.

---

## Account Isolation

Using separate AWS accounts provides a stronger isolation boundary than simply separating workloads inside one account.

Example:

```text
One Account
 ├── Dev
 └── Prod
```

versus:

```text
Organization
 ├── Dev Account
 └── Prod Account
```

The second design provides stronger separation between environments.

---

## Security Account

Organizations commonly use dedicated accounts for security-related functions.

Example:

```text
Organization
 |
 ├── Management
 ├── Security
 ├── Logging
 ├── Development
 └── Production
```

A separate security account can help centralize security tooling and reduce the risk of mixing security infrastructure with application workloads.

---

## Logging Account

A dedicated logging account can be used to centralize certain logs and security data.

For example:

```text
Dev Account ─────┐
                 |
Prod Account ────┼──→ Central Logging
                 |
Test Account ────┘
```

This can improve monitoring and security operations in larger environments.

---

## AWS Organizations and IAM Identity Center

AWS Organizations can work with **IAM Identity Center** to provide centralized access to multiple AWS accounts.

Conceptually:

```text
User
 |
 ↓
IAM Identity Center
 |
 ├── Development Account
 ├── Testing Account
 └── Production Account
```

Administrators can manage access to multiple accounts from a central location.

This is generally preferable to creating separate IAM users in every account for large organizations.

---

## Organizations vs IAM

These services solve different problems.

| Service             | Main Purpose                                 |
| ------------------- | -------------------------------------------- |
| IAM                 | Access control within an AWS account         |
| Organizations       | Central management of multiple AWS accounts  |
| SCP                 | Organization-level permission restrictions   |
| IAM Identity Center | Centralized workforce access to AWS accounts |

Example:

```text
Organizations
      |
      ├── Dev Account
      ├── Test Account
      └── Prod Account
             |
             ↓
            IAM
```

---

## Organizations and VPCs

Organizations does not replace VPCs.

They operate at different levels.

```text
AWS Organization
       |
       ├── Account A
       │     └── VPC
       |
       └── Account B
             └── VPC
```

Each AWS account can have its own VPCs and networking architecture.

If accounts need to communicate with each other, services such as **VPC Peering** or **Transit Gateway** can be used depending on the architecture.

---

## AWS Organizations and Terraform

Organizations can also be managed using Terraform.

For example, Terraform can be used to define:

* AWS accounts
* Organizational Units
* SCPs
* Account relationships
* Organization structure

Conceptually:

```text
Terraform
    |
    ↓
AWS Organizations
    |
 ┌──┼────────┐
 ↓  ↓        ↓
OUs Accounts SCPs
```

This can make large multi-account environments more consistent and repeatable.

---

## Example Enterprise Architecture

A larger company might use:

```text
AWS Organization
│
├── Management Account
│
├── Security OU
│   ├── Security Account
│   └── Logging Account
│
├── Infrastructure OU
│   └── Shared Services Account
│
├── Development OU
│   └── Development Account
│
└── Production OU
    ├── Application Account
    └── Database Account
```

This provides separation between different responsibilities and environments.

---

## AWS Organizations in SAA Scenarios

Organizations is useful when a question describes:

* Multiple AWS accounts
* Centralized account management
* Consolidated billing
* Organization-wide governance
* Restricting services or regions across accounts
* Separating production and development
* Applying policies to groups of accounts
* Multi-account security architecture

### Common scenario

> A company has multiple AWS accounts and wants to prevent all development accounts from launching resources in an unauthorized AWS Region.

A suitable solution is:

```text
Development Accounts
        ↓
Development OU
        ↓
SCP
        ↓
Restrict Unauthorized Region
```

---

## Important Exam Distinctions

### Organizations vs SCP

```text
Organizations
    ↓
Manages multiple accounts

SCP
    ↓
Restricts maximum permissions
```

### SCP vs IAM

```text
SCP
    ↓
Organization/account-level guardrail

IAM
    ↓
User/role/resource permissions
```

### OU vs Account

```text
OU
    ↓
Logical grouping

Account
    ↓
Actual AWS account
```

---

## Key Points

* AWS Organizations centrally manages multiple AWS accounts.
* A management account controls the organization.
* Member accounts belong to the organization.
* Organizational Units group accounts logically.
* SCPs provide organization-level permission guardrails.
* **SCPs do not grant permissions.**
* An explicit deny overrides an allow.
* Organizations supports consolidated billing.
* Separate accounts provide stronger security and environment isolation.
* IAM manages permissions within accounts; Organizations manages accounts and governance across them.
* IAM Identity Center can provide centralized workforce access to multiple AWS accounts.
* Organizations is especially useful for enterprise multi-account AWS architectures.
* For your current Sheepeye project, you **do not need AWS Organizations**; it becomes relevant when managing multiple AWS accounts/environments.
