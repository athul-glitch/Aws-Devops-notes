# AWS CloudTrail

## What is AWS CloudTrail?

AWS CloudTrail is a service that **records and tracks API activity and actions performed in an AWS account**.

It helps answer questions such as:

* Who made a change?
* What action was performed?
* Which AWS resource was affected?
* When did the action happen?
* From where did the request originate?

Conceptually:

```text
User / Application
        |
        ↓
    AWS API Call
        |
        ↓
   AWS CloudTrail
        |
        ↓
 Logs / Events
```

---

# Why CloudTrail Matters

CloudTrail is mainly used for:

* Auditing
* Security investigation
* Troubleshooting
* Compliance
* Tracking configuration changes
* Investigating suspicious activity

Example:

Someone deletes an EC2 security group.

CloudTrail can help identify:

```text
Who     → IAM user/role
Action  → DeleteSecurityGroup
When    → Timestamp
Resource → Security Group
Source  → Request information
```

---

# CloudTrail Events

An **event** represents an activity recorded by CloudTrail.

Examples:

* Create an EC2 instance
* Stop an EC2 instance
* Delete an S3 bucket
* Modify an IAM policy
* Create a VPC
* Change a security group
* Assume an IAM role

Conceptually:

```text
API Request
    ↓
CloudTrail Event
    ↓
Event Record
```

---

# Management Events

Management events record control-plane operations performed on AWS resources.

Examples:

```text
CreateInstance
TerminateInstances
CreateBucket
CreateRole
PutRolePolicy
CreateVpc
DeleteSecurityGroup
```

These events are particularly useful for auditing configuration and administrative changes.

---

# Data Events

Data events provide more detailed information about operations performed on or within certain resources.

For example, S3 object-level activity can include:

```text
GetObject
PutObject
DeleteObject
```

Data events can generate significantly more events than management events, so they should be enabled selectively based on monitoring requirements and cost considerations.

---

# Insights Events

CloudTrail Insights can help identify unusual patterns of API activity.

For example:

```text
Normal API Activity
       ↓
Sudden unusual increase
       ↓
CloudTrail Insights
       ↓
Potential investigation
```

This can help identify abnormal operational behavior.

---

# CloudTrail and IAM

CloudTrail records actions made through AWS APIs, including actions performed using IAM identities.

Example:

```text
IAM Role
   |
   ↓
Start EC2 Instance
   |
   ↓
CloudTrail
   |
   ↓
Event
```

This makes CloudTrail useful when investigating who performed an operation.

---

# CloudTrail and S3

CloudTrail logs can be delivered to an S3 bucket for long-term storage.

Conceptually:

```text
AWS Account
    |
    ↓
CloudTrail
    |
    ↓
S3 Bucket
    |
    ↓
Long-term Logs
```

S3 provides durable storage for audit logs.

---

# CloudTrail and CloudWatch Logs

CloudTrail events can be sent to CloudWatch Logs for monitoring and analysis.

```text
CloudTrail
    |
    ↓
CloudWatch Logs
    |
    ↓
Monitoring / Alerts
```

For example, an organization could monitor sensitive IAM activity and create alerts when important changes occur.

---

# CloudTrail and EventBridge

CloudTrail events can be used with EventBridge to create event-driven automation.

Example:

```text
IAM Policy Changed
       ↓
   CloudTrail
       ↓
  EventBridge
       ↓
     Rule
       ↓
    Lambda
       ↓
Notification / Action
```

This is useful when an organization wants to automatically respond to certain AWS activities.

---

# CloudTrail vs CloudWatch

These services are commonly confused.

### CloudTrail

Focuses on:

**Who did what in AWS?**

```text
User
 ↓
API Call
 ↓
CloudTrail
```

Example:

> Who terminated this EC2 instance?

---

### CloudWatch

Focuses on:

**How are my AWS resources and applications performing?**

```text
EC2
 ↓
CloudWatch
 ↓
CPU / Memory-related monitoring / Logs / Alarms
```

Example:

> Is this EC2 instance experiencing high CPU usage?

Simple rule:

```text
CloudTrail → API activity / auditing

CloudWatch → Monitoring / metrics / logs / alarms
```

---

# CloudTrail vs AWS Config

CloudTrail and AWS Config also have different purposes.

### CloudTrail

Records **actions and API activity**.

```text
Who changed it?
When?
What API call?
```

### AWS Config

Records and evaluates **resource configuration and compliance**.

```text
What is the current configuration?
Is it compliant?
How did configuration change over time?
```

Simple comparison:

```text
CloudTrail → Activity history

AWS Config → Configuration history
```

---

# CloudTrail Organization Trail

AWS Organizations can be used with CloudTrail to create centralized auditing across multiple AWS accounts.

Conceptually:

```text
AWS Organization
       |
       ↓
Management Account
       |
       ↓
CloudTrail
       |
       ├── Account A
       ├── Account B
       └── Account C
```

This is useful for centralized security and compliance monitoring.

---

# Multi-Region Trails

A CloudTrail trail can be configured to record activity across AWS Regions.

This is important because an organization may use resources in multiple Regions.

Example:

```text
CloudTrail
   |
   ├── ap-south-1
   ├── us-east-1
   └── eu-west-1
```

A centralized trail provides broader visibility across the AWS environment.

---

# CloudTrail Log File Validation

CloudTrail supports log file integrity validation.

This can help determine whether CloudTrail log files have been modified after delivery.

This is particularly useful for security and compliance requirements.

---

# Security Use Case

Suppose an IAM policy was unexpectedly modified.

A security engineer can investigate:

```text
Unexpected IAM Change
        ↓
CloudTrail
        ↓
Find API Event
        ↓
Identify Identity
        ↓
Check Timestamp
        ↓
Investigate
```

CloudTrail therefore provides an important audit trail for AWS activity.

---

# Example: EC2 Terminated Unexpectedly

Suppose an EC2 instance suddenly disappears.

CloudTrail can be used to investigate:

```text
EC2 Instance Terminated
        ↓
Search CloudTrail
        ↓
TerminateInstances event
        ↓
Identify IAM identity
        ↓
Check request details
        ↓
Investigate
```

CloudWatch would be more appropriate for investigating the instance's performance before the termination.

---

# Example: S3 Object Activity

Suppose sensitive data was unexpectedly deleted from S3.

If appropriate S3 data events have been enabled:

```text
DeleteObject
     ↓
CloudTrail
     ↓
Identify activity
     ↓
Investigate
```

Data events should be enabled deliberately because they can produce a high volume of events.

---

# CloudTrail and Security Monitoring

CloudTrail can form part of a larger AWS security monitoring architecture.

Example:

```text
AWS Activity
     ↓
CloudTrail
     ↓
CloudWatch / EventBridge
     ↓
Detection / Alert
     ↓
Security Team
```

For larger environments, CloudTrail data can also be integrated with security and SIEM systems.

---

# CloudTrail Best Practices

### 1. Enable CloudTrail

Maintain audit visibility for AWS activity.

### 2. Use centralized logging

For multiple AWS accounts, consider centralized log storage.

### 3. Protect log storage

Use appropriate S3 security controls and access policies.

### 4. Enable important data events selectively

Monitor sensitive resources where object-level activity matters.

### 5. Monitor critical API activity

Examples:

* IAM changes
* Security group changes
* CloudTrail configuration changes
* KMS changes
* S3 security changes

### 6. Use CloudWatch/EventBridge for automation

Important events can trigger alerts or automated responses.

---

# CloudTrail and KMS

CloudTrail can record API activity involving AWS services such as KMS.

Example:

```text
KMS Key Activity
       ↓
CloudTrail
       ↓
Audit Record
```

This can be useful when investigating security-sensitive operations.

---

# CloudTrail and Sheepeye

CloudTrail is useful for your Sheepeye AWS infrastructure even though it does not directly handle application traffic.

For example:

```text
Sheepeye Infrastructure
        |
        ↓
AWS API Activity
        |
        ↓
CloudTrail
        |
        ↓
Audit Trail
```

If someone changes:

* IAM permissions
* EC2 configuration
* Security groups
* VPC configuration
* S3 settings
* CloudTrail settings

CloudTrail can provide an audit record of the API activity.

---

# CloudTrail and Terraform

Terraform makes AWS API calls when creating or modifying infrastructure.

Conceptually:

```text
Terraform
    ↓
AWS API
    ↓
Infrastructure Change
    ↓
CloudTrail Event
```

Therefore, CloudTrail can help audit infrastructure changes made through Terraform.

For example:

```text
terraform apply
      ↓
AWS API calls
      ↓
CloudTrail
      ↓
Audit trail
```

---

# Important SAA Concepts

### CloudTrail

Think:

> **API activity and auditing**

### CloudWatch

Think:

> **Monitoring, metrics, logs and alarms**

### AWS Config

Think:

> **Resource configuration and compliance**

### EventBridge

Think:

> **Event routing and automation**

A useful memory table:

| Service     | Main Purpose                         |
| ----------- | ------------------------------------ |
| CloudTrail  | API activity / auditing              |
| CloudWatch  | Monitoring / metrics / logs / alarms |
| AWS Config  | Configuration / compliance           |
| EventBridge | Event routing / automation           |
| S3          | Durable object storage               |

---

# SAA Scenario

> A company wants to know which IAM identity terminated an EC2 instance.

Use:

```text
AWS CloudTrail
```

---

# SAA Scenario

> A company wants an alert when someone modifies a critical security group.

A possible architecture is:

```text
Security Group Change
        ↓
CloudTrail
        ↓
EventBridge
        ↓
Rule
        ↓
SNS / Lambda
```

---

# SAA Scenario

> A company needs long-term centralized storage of audit logs.

A common architecture is:

```text
CloudTrail
    ↓
S3
    ↓
Long-term Audit Storage
```

---

# Key Points

* **CloudTrail records AWS API activity.**
* It helps answer **who did what and when**.
* Management events record control-plane operations.
* Data events provide more detailed resource-level activity for supported services.
* CloudTrail Insights can identify unusual API activity patterns.
* CloudTrail logs can be stored in S3.
* CloudTrail can integrate with CloudWatch and EventBridge.
* CloudTrail is important for security, auditing and compliance.
* CloudTrail can provide visibility across multiple AWS accounts and Regions.
* **CloudTrail = API activity/auditing.**
* **CloudWatch = monitoring and observability.**
* **AWS Config = resource configuration/compliance.**
* **EventBridge = event routing and automation.**

