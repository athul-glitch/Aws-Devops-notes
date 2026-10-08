# AWS Identity and Access Management (IAM)

## What is IAM?

AWS Identity and Access Management (IAM) is used to control **who can access AWS resources and what actions they can perform**.

IAM is commonly used to manage:

* Users
* Groups
* Roles
* Policies
* Permissions
* Authentication and authorization

Example:

```text
IAM User / Role
       |
   IAM Policy
       |
   AWS Resource
```

---

## Authentication vs Authorization

### Authentication

Authentication verifies **who you are**.

Example:

```text
Username + Password
MFA
Access Keys
```

### Authorization

Authorization determines **what you are allowed to do**.

Example:

```text
Can this user create an EC2 instance?
Can this role read from an S3 bucket?
```

IAM mainly controls authorization through policies.

---

## IAM Users

An IAM user represents a person or application that needs access to AWS.

A user can have:

* Console access
* Access keys
* Permissions through policies

Example:

```text
Developer
    |
IAM User
    |
Permissions
    |
AWS Resources
```

For human users, AWS generally recommends using centralized identity solutions or federation where appropriate rather than creating many long-term IAM users.

---

## IAM Groups

An IAM group is a collection of IAM users.

Groups make it easier to manage permissions for multiple users.

Example:

```text
Developers Group
       |
   ┌───┼───┐
   ↓   ↓   ↓
 User User User
```

A policy can be attached to the group instead of assigning the same policy individually to every user.

---

## IAM Roles

An IAM role provides permissions that can be **assumed** by trusted identities or AWS services.

Roles are commonly used for:

* EC2 instances
* Lambda functions
* Cross-account access
* AWS services
* Temporary access

Example:

```text
EC2
 |
IAM Role
 |
IAM Policy
 |
S3
```

An EC2 instance can use an IAM role to access S3 without storing AWS access keys on the server.

---

## IAM Policies

IAM policies are JSON documents that define permissions.

A policy specifies things such as:

* Effect
* Action
* Resource
* Conditions

Example:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::example-bucket/*"
}
```

This policy allows reading objects from the specified S3 bucket.

---

## Effect

The `Effect` determines whether an action is allowed or denied.

Common values:

```text
Allow
Deny
```

Example:

```json
{
  "Effect": "Allow"
}
```

An explicit `Deny` takes precedence over an `Allow`.

---

## Actions

The `Action` specifies what AWS API operations are allowed or denied.

Examples:

```text
ec2:StartInstances
ec2:StopInstances
s3:GetObject
s3:PutObject
s3:ListBucket
```

A policy can allow one action or multiple actions.

---

## Resources

The `Resource` specifies which AWS resources the policy applies to.

Example:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::example-bucket/*"
}
```

The `*` means all matching resources in the specified scope.

---

## Least Privilege

**Least privilege** means giving an identity only the permissions it actually needs.

Bad example:

```text
Application
    |
AdministratorAccess
```

Better:

```text
Application
    |
Limited IAM Role
    |
Only required permissions
```

For example, if an application only needs to read S3 objects, it should not receive permission to delete buckets or create EC2 instances.

Least privilege is an important AWS security principle.

---

## IAM Policy Types

### Identity-Based Policies

Attached to identities such as:

* Users
* Groups
* Roles

Example:

```text
IAM Role
   |
IAM Policy
```

### Resource-Based Policies

Attached directly to resources.

Examples include:

* S3 bucket policies
* SQS queue policies
* SNS topic policies

Example:

```text
S3 Bucket
    |
Bucket Policy
```

---

## IAM Role Trust Policy

An IAM role has a **trust policy** that defines who or what is allowed to assume the role.

Example:

```text
EC2
 |
Can assume
 |
IAM Role
 |
Permissions
```

The trust policy answers:

> Who can assume this role?

The permissions policy answers:

> What can the role do after assuming it?

These are different concepts.

---

## IAM and EC2

EC2 instances can use IAM roles to access AWS services.

Example:

```text
EC2 Instance
      |
      ↓
IAM Role
      |
      ↓
Permission to read S3
      |
      ↓
S3 Bucket
```

This is preferred over storing long-term AWS access keys inside an EC2 server.

---

## IAM and S3

IAM can control access to S3 resources.

Example:

```text
Application
     |
IAM Role
     |
s3:GetObject
     |
S3 Bucket
```

An application may be allowed to read objects while being denied permission to delete them.

---

## MFA

Multi-Factor Authentication (MFA) adds another layer of security.

Instead of relying only on a password, the user must provide an additional authentication factor.

Example:

```text
Password
   +
MFA
   ↓
AWS Console
```

MFA is especially important for privileged accounts.

---

## Access Keys

Access keys are credentials used for programmatic AWS access.

They consist of:

```text
Access Key ID
Secret Access Key
```

They may be used with:

* AWS CLI
* SDKs
* Applications

Long-term access keys should be avoided where temporary credentials or IAM roles can be used.

**Never commit access keys to GitHub.**

---

## AWS Root User

The AWS account root user has extensive permissions over the AWS account.

Recommended security practices include:

* Enable MFA
* Do not use the root user for everyday tasks
* Do not create root access keys
* Use IAM roles/users for normal administration

The root user should generally be used only for tasks that specifically require root access.

---

## IAM Policy Evaluation

AWS evaluates policies to determine whether an action is allowed.

A simplified model is:

```text
Request
   |
Policy Evaluation
   |
Explicit Deny?
   |
   ├── Yes → Denied
   |
   └── No
        |
      Allow?
        |
     ┌──┴──┐
    Yes    No
     ↓      ↓
  Allowed  Denied
```

An explicit `Deny` overrides an `Allow`.

---

## Cross-Account Access

IAM roles can be used to allow access between AWS accounts.

Example:

```text
Account A
Developer
    |
Assume Role
    ↓
Account B
IAM Role
    |
AWS Resources
```

This allows controlled access without sharing long-term credentials between accounts.

---

## IAM Best Practices

* Follow the principle of least privilege.
* Enable MFA for important accounts.
* Avoid using the root user for everyday tasks.
* Avoid long-term access keys when roles can be used.
* Never commit AWS credentials to GitHub.
* Review unused permissions.
* Use roles for AWS services such as EC2 and Lambda.
* Give users and applications only the permissions they need.

---

## IAM and Sheepeye

IAM can be used in Sheepeye to securely allow AWS services to interact with each other.

Example:

```text
Sheepeye EC2
     |
IAM Role
     |
     ├── CloudWatch permissions
     |
     └── SNS permissions
```

This allows the application/server to use AWS services without storing AWS secret keys inside the application.

---

## Key Points

* IAM controls **authentication and authorization** in AWS.
* Users represent identities.
* Groups organize users and permissions.
* Roles provide temporary or assumable permissions.
* Policies define permissions.
* Least privilege improves security.
* IAM roles are preferred for AWS services such as EC2.
* Identity-based policies are attached to identities.
* Resource-based policies are attached to resources.
* Explicit `Deny` overrides `Allow`.
* MFA improves account security.
* Never expose AWS credentials or commit them to GitHub.
* IAM is a fundamental AWS security service.
