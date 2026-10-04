# AWS Notes

Notes and practical concepts learned while studying Amazon Web Services.

## ☁️ AWS Fundamentals

### Regions and Availability Zones

* **Region** — A geographical area containing multiple AWS Availability Zones.
* **Availability Zone (AZ)** — An isolated data center location within an AWS Region.
* Deploying across multiple AZs can improve availability and fault tolerance.

Example:

```text
AWS Region
│
├── Availability Zone A
├── Availability Zone B
└── Availability Zone C
```

---

# Amazon EC2

Amazon EC2 provides virtual servers in the AWS cloud.

## Important Concepts

* AMI
* Instance types
* Key pairs
* Security Groups
* EBS
* Elastic IP
* User Data
* Instance states

### Basic EC2 flow

```text
AMI
 ↓
Launch Instance
 ↓
Choose Instance Type
 ↓
Configure Network
 ↓
Security Group
 ↓
Connect using SSH
```

---

# Amazon VPC

Amazon VPC provides a logically isolated network for AWS resources.

## Important Components

* VPC
* Subnets
* Route Tables
* Internet Gateway
* NAT Gateway
* Security Groups
* Network ACLs

Example:

```text
VPC
│
├── Public Subnet
│   └── EC2
│
└── Private Subnet
    └── Database
```

## Public Subnet

A subnet is considered public when its route table has a route to an Internet Gateway.

## Private Subnet

A private subnet does not have a direct route to an Internet Gateway.

---

# Security Groups

Security Groups act as virtual firewalls for AWS resources such as EC2.

Important points:

* Control inbound traffic
* Control outbound traffic
* Rules are stateful
* Allow rules can be configured
* No explicit deny rules

Example:

```text
SSH
TCP 22
Source: My IP

HTTP
TCP 80
Source: 0.0.0.0/0

HTTPS
TCP 443
Source: 0.0.0.0/0
```

---

# Amazon S3

Amazon S3 is an object storage service.

Important concepts:

* Buckets
* Objects
* Object keys
* Versioning
* Bucket policies
* Encryption
* Storage classes

Example:

```text
S3 Bucket
│
├── image.jpg
├── document.pdf
└── website/
    ├── index.html
    └── style.css
```

---

# AWS IAM

IAM controls access to AWS resources.

Main components:

* Users
* Groups
* Roles
* Policies

### IAM Policy

Policies are JSON documents that define permissions.

Example concept:

```text
User / Application
       ↓
IAM Role
       ↓
IAM Policy
       ↓
AWS Service
```

---

# Amazon RDS

Amazon RDS is a managed relational database service.

Supported database engines include:

* PostgreSQL
* MySQL
* MariaDB
* Oracle
* SQL Server
* Aurora

Important concepts:

* DB instance
* Automated backups
* Multi-AZ
* Security Groups
* Storage
* Endpoint

---

# Amazon CloudWatch

CloudWatch is used for monitoring and observability.

It can collect:

* Metrics
* Logs
* Alarms
* Events

Example:

```text
EC2
 ↓
CloudWatch Metrics
 ↓
Alarm
 ↓
Action / Notification
```

---

# Amazon SNS

Amazon SNS is a notification service.

Common use cases:

* Email notifications
* Application alerts
* Monitoring notifications
* Fan-out messaging

Example:

```text
Application
    ↓
SNS Topic
    ↓
Email / Other Subscribers
```

In the Sheepeye project, SNS is used for booking notification alerts.

---

# Route 53

Amazon Route 53 is AWS's DNS service.

Common uses:

* Domain name resolution
* DNS records
* Domain routing
* Health checks

Example:

```text
sheepeye.shop
      ↓
Route 53
      ↓
AWS Resource
```

---

# CloudFront

Amazon CloudFront is AWS's Content Delivery Network (CDN).

It can cache content at edge locations closer to users.

Basic flow:

```text
User
 ↓
CloudFront
 ↓
Origin
 ├── S3
 └── Application Server
```

---

# Application Load Balancer

An Application Load Balancer distributes HTTP/HTTPS traffic between targets.

Example:

```text
Users
  ↓
ALB
  ↓
├── EC2 Instance 1
└── EC2 Instance 2
```

Useful for:

* Load distribution
* High availability
* Health checks
* HTTP/HTTPS applications

---

# Auto Scaling

EC2 Auto Scaling can automatically adjust the number of EC2 instances according to demand.

Example:

```text
Low Traffic
    ↓
2 EC2 Instances

High Traffic
    ↓
4 EC2 Instances
```

Auto Scaling commonly works together with:

* Launch Templates
* Application Load Balancer
* CloudWatch

---

# AWS EFS

Amazon Elastic File System (EFS) provides shared file storage that can be mounted by multiple EC2 instances.

Example:

```text
EC2-1 ──┐
        │
EC2-2 ──┼── EFS
        │
EC2-3 ──┘
```

Useful when multiple servers need access to the same files.

---

# AWS Organizations

AWS Organizations allows multiple AWS accounts to be centrally managed.

Important concepts:

* Management account
* Member accounts
* Organizational Units (OUs)
* Service Control Policies (SCPs)
* Centralized billing

---

# AWS CloudFormation

CloudFormation is an Infrastructure as Code service from AWS.

Infrastructure can be defined using templates, commonly YAML or JSON.

Example:

```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
```

CloudFormation then creates and manages the defined AWS resources.

---

# Practical Learning

These AWS concepts are being applied through my **Sheepeye 2 AWS Cloud & DevOps project**.

Current hands-on work includes:

* VPC creation
* Subnet provisioning
* EC2 deployment
* Linux server configuration
* Security Groups
* Terraform-based infrastructure

Additional AWS services are being studied and introduced incrementally.
