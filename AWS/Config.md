# AWS Config

## What is AWS Config?

AWS Config is a service that **records and evaluates the configuration of AWS resources**.

It helps answer questions such as:

* What is the current configuration of this resource?
* How has the configuration changed?
* Is the resource compliant with our rules?
* Which resources violate security or compliance requirements?

Conceptually:

```text
AWS Resources
      |
      ↓
  AWS Config
      |
      ├── Configuration History
      ├── Configuration Changes
      └── Compliance Evaluation
```

---

# Why Use AWS Config?

AWS Config is mainly used for:

* Configuration tracking
* Compliance
* Security auditing
* Change management
* Resource inventory
* Detecting configuration violations

Example:

A company requires all S3 buckets to block public access.

AWS Config can evaluate the buckets against this requirement.

```text
S3 Bucket
    ↓
AWS Config Rule
    ↓
Public access blocked?
    ↓
YES → COMPLIANT
NO  → NON_COMPLIANT
```

---

# Configuration Item

A **Configuration Item (CI)** represents the configuration state of an AWS resource at a particular point in time.

It can contain information about things such as:

* Resource type
* Resource ID
* Configuration
* Relationships
* Timestamp

Example:

```text
EC2 Instance
     ↓
Configuration Item
     ↓
Instance configuration at a specific time
```

When the resource configuration changes, AWS Config can record the new configuration state.

---

# Configuration History

AWS Config can maintain a history of configuration changes.

Example:

```text
Security Group
     |
     ├── 10:00 → Port 22 restricted
     ├── 12:00 → Port 80 added
     └── 14:00 → Port 22 changed
```

This helps administrators understand how a resource's configuration changed over time.

---

# Configuration Recorder

The AWS Config configuration recorder records configuration information about supported AWS resources.

Conceptually:

```text
AWS Resource
     ↓
Configuration Recorder
     ↓
AWS Config
     ↓
Configuration Data
```

The recorder needs to be configured for the resource types you want AWS Config to track.

---

# Configuration Rules

AWS Config Rules evaluate AWS resources against desired configuration requirements.

A rule can determine whether a resource is:

* COMPLIANT
* NON_COMPLIANT

Example:

```text
Rule:
Security groups should not allow unrestricted SSH

        ↓

Security Group
        ↓
    Evaluation
        ↓
COMPLIANT / NON_COMPLIANT
```

---

# Managed Rules

AWS provides many predefined Config rules for common compliance requirements.

Examples can check whether:

* S3 buckets are publicly accessible
* EBS volumes are encrypted
* IAM resources follow certain requirements
* Security groups expose restricted ports
* Resources have required configurations

Using managed rules can reduce the need to create custom logic.

---

# Custom Config Rules

Organizations can also create custom Config rules when built-in rules do not satisfy their requirements.

Custom evaluation logic can be implemented using AWS Lambda.

Conceptually:

```text
AWS Resource
      ↓
AWS Config
      ↓
Custom Rule
      ↓
Lambda
      ↓
Evaluation
```

---

# Compliance Status

AWS Config rules can report compliance status.

Example:

```text
EC2-01 → COMPLIANT
S3-01  → COMPLIANT
SG-01  → NON_COMPLIANT
```

This makes it easier to identify resources that require attention.

---

# Remediation

AWS Config can be used with remediation actions to help correct non-compliant resources.

Conceptually:

```text
Resource
   ↓
Config Rule
   ↓
NON_COMPLIANT
   ↓
Remediation
   ↓
Correct Configuration
```

Remediation can involve AWS automation services such as Systems Manager Automation or other supported actions.

---

# AWS Config vs CloudTrail

These two services are commonly confused.

### CloudTrail

CloudTrail focuses on **API activity**.

It answers:

> Who performed an AWS API action?

```text
User
 ↓
API Call
 ↓
CloudTrail
```

### AWS Config

Config focuses on **resource configuration and compliance**.

It answers:

> What is the resource configured like, and is it compliant?

```text
Resource
 ↓
AWS Config
 ↓
Configuration / Compliance
```

Simple rule:

```text
CloudTrail → Activity

Config → Configuration
```

---

# AWS Config vs CloudWatch

### CloudWatch

Used for:

* Metrics
* Logs
* Alarms
* Application monitoring
* Operational monitoring

Example:

```text
EC2
 ↓
CloudWatch
 ↓
CPU Utilization
```

### AWS Config

Used for:

* Resource configuration
* Configuration history
* Compliance

Example:

```text
Security Group
 ↓
AWS Config
 ↓
Is SSH publicly accessible?
```

Simple rule:

```text
CloudWatch → How is it performing?

Config → How is it configured?
```

---

# AWS Config and CloudTrail Together

These services complement each other.

Suppose a security group suddenly becomes publicly accessible.

```text
Security Group
      |
      ├── AWS Config
      |      ↓
      |   Configuration changed
      |
      └── CloudTrail
             ↓
          API activity
             ↓
        Identify who made change
```

Config helps identify **what changed**.

CloudTrail helps identify **who performed the API action**.

---

# AWS Config and EventBridge

AWS Config events can be used with EventBridge.

Example:

```text
Resource becomes NON_COMPLIANT
          ↓
      AWS Config
          ↓
      EventBridge
          ↓
         Rule
          ↓
    SNS / Lambda
          ↓
       Alert
```

This allows organizations to respond automatically to configuration violations.

---

# AWS Config and SNS

SNS can be used to notify administrators when a resource becomes non-compliant.

Example:

```text
S3 Bucket
   ↓
Config Rule
   ↓
NON_COMPLIANT
   ↓
EventBridge
   ↓
SNS
   ↓
Security Team
```

---

# AWS Config and S3

AWS Config can use S3 for storing configuration history and related data.

Conceptually:

```text
AWS Config
    ↓
S3
    ↓
Configuration Data
```

S3 provides durable storage for this information.

---

# AWS Config Aggregator

In larger environments, AWS Config data can be aggregated from multiple AWS accounts and Regions.

Conceptually:

```text
             Config Aggregator
                    |
       ┌────────────┼────────────┐
       ↓            ↓            ↓
   Account A    Account B    Account C
       |            |            |
     Config       Config       Config
```

This is useful for centralized compliance visibility across an organization.

---

# Multi-Account Compliance

Consider a company with several AWS accounts:

```text
Production
Development
Testing
Security
```

AWS Config can provide configuration and compliance information across these environments.

This helps a central security or cloud team monitor organizational requirements.

---

# Resource Relationships

AWS Config can also track relationships between resources.

For example:

```text
EC2
 ↓
Security Group
 ↓
VPC
 ↓
Subnet
```

Understanding resource relationships can help when investigating configuration changes.

---

# Example: EBS Encryption Requirement

Suppose a company requires all EBS volumes to be encrypted.

A Config rule can evaluate the resources.

```text
EBS Volume
     ↓
Config Rule
     ↓
Encrypted?
  /       \
YES       NO
 ↓         ↓
COMPLIANT  NON_COMPLIANT
```

This is a typical compliance use case.

---

# Example: Security Group Requirement

Suppose SSH should not be open to the entire internet.

```text
Security Group
      ↓
Config Rule
      ↓
Check SSH
      ↓
0.0.0.0/0 ?
   /       \
 YES        NO
  ↓          ↓
NON-        COMPLIANT
COMPLIANT
```

This helps detect insecure configurations.

---

# Example: S3 Public Access

Suppose an organization requires all S3 buckets to prevent public access.

```text
S3 Bucket
   ↓
Config Rule
   ↓
Check Public Access
   ↓
COMPLIANT / NON_COMPLIANT
```

This is a common AWS compliance scenario.

---

# AWS Config and Security

AWS Config is useful for security because it can continuously evaluate resource configurations.

For example:

```text
AWS Environment
      ↓
AWS Config
      ↓
Security Rules
      ↓
Compliance Status
```

This provides continuous configuration visibility rather than relying only on manual checks.

---

# AWS Config and Terraform

Terraform manages infrastructure configuration, while AWS Config evaluates the actual AWS resource configuration.

Conceptually:

```text
Terraform
   ↓
Desired Infrastructure
   ↓
AWS Resources
   ↓
AWS Config
   ↓
Actual Configuration / Compliance
```

This is useful because Terraform defines what you intend to deploy, while Config can help verify whether deployed resources meet defined compliance requirements.

---

# AWS Config and Sheepeye

AWS Config can be a useful **future security/compliance layer** for your Sheepeye infrastructure.

For example, you could evaluate:

* Security group configuration
* S3 configuration
* IAM-related resources
* EBS encryption
* Resource configuration

Example:

```text
Sheepeye AWS Infrastructure
            ↓
        AWS Config
            ↓
     Configuration Rules
            ↓
   COMPLIANT / NON_COMPLIANT
```

You don't need to add Config immediately to Sheepeye. It is more important to first complete the core infrastructure and Terraform work.

---

# Important SAA Scenarios

## Scenario 1: Track configuration changes

> A company wants to track how the configuration of AWS resources changes over time.

Use:

```text
AWS Config
```

---

## Scenario 2: Identify non-compliant resources

> A company wants to ensure all EBS volumes are encrypted.

Use:

```text
AWS Config Rules
```

---

## Scenario 3: Find who made a configuration change

> A security team needs to know who modified a security group.

Use:

```text
CloudTrail
```

CloudTrail records the API activity.

---

## Scenario 4: Monitor compliance across accounts

> A company has many AWS accounts and wants centralized compliance visibility.

Use:

```text
AWS Config Aggregator
```

---

## Scenario 5: Automatically respond to violations

> When a resource becomes non-compliant, the company wants to trigger an automated response.

Possible architecture:

```text
AWS Config
    ↓
EventBridge
    ↓
Lambda / Automation
    ↓
Remediation
```

---

# Key Points

* **AWS Config tracks resource configuration and compliance.**
* It records configuration information about supported AWS resources.
* Configuration Items represent resource configuration states.
* Configuration history helps track changes over time.
* Config Rules evaluate resources against desired requirements.
* Rules can report resources as **COMPLIANT** or **NON_COMPLIANT**.
* AWS provides managed rules for common requirements.
* Custom rules can use Lambda for custom evaluation logic.
* Config can be used with automated remediation.
* Config data can be aggregated across AWS accounts and Regions.
* **CloudTrail = who performed an API action.**
* **AWS Config = what the resource is configured like and whether it is compliant.**
* **CloudWatch = resource/application monitoring.**
* For SAA, think **AWS Config → configuration + compliance**.
