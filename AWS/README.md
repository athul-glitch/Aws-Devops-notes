# AWS Notes

My practical AWS notes for cloud infrastructure, AWS Solutions Architect Associate (SAA-C03) preparation, and junior cloud support roles.

## AWS Fundamentals

- AWS Global Infrastructure
- Regions and Availability Zones
- Shared Responsibility Model
- AWS Pricing and Billing
- IAM
- AWS Organizations

## Compute

- Amazon EC2
- AMIs
- Instance Types
- EBS
- Elastic IP
- User Data
- EC2 Instance Store
- Auto Scaling
- Elastic Load Balancing
- Lambda

## Networking

- Amazon VPC
- Public and Private Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Network ACLs
- VPC Peering
- Transit Gateway
- VPC Endpoints
- Route 53

## Storage

- Amazon S3
- S3 Storage Classes
- S3 Lifecycle
- S3 Versioning
- S3 Encryption
- EFS
- EBS

## Databases

- Amazon RDS
- Amazon Aurora
- RDS Multi-AZ
- Read Replicas
- Database Backups

## Monitoring

- Amazon CloudWatch
- CloudWatch Metrics
- CloudWatch Logs
- CloudWatch Alarms
- AWS CloudTrail

## Application Services

- SNS
- SQS
- SES
- CloudFront

## Management & Infrastructure

- AWS CloudFormation
- AWS Systems Manager
- AWS Trusted Advisor

## Security

- IAM Users
- IAM Groups
- IAM Roles
- IAM Policies
- Least Privilege
- MFA
- KMS
- Security Groups vs NACLs

## Important AWS Comparisons

- Security Group vs NACL
- EBS vs EFS
- S3 vs EBS
- RDS Multi-AZ vs Read Replica
- NAT Gateway vs Internet Gateway
- SNS vs SQS
- CloudWatch vs CloudTrail
- ALB vs NLB
- Public Subnet vs Private Subnet

## SAA Scenario Practice

### Scenario 1 — Private EC2 needs internet access

An EC2 instance in a private subnet needs outbound internet access.

**Solution:** Use a NAT Gateway in a public subnet and route the private subnet's traffic through it.

### Scenario 2 — Highly available application

An application needs to remain available if one Availability Zone fails.

**Solution:** Deploy resources across multiple Availability Zones and use an Application Load Balancer with Auto Scaling.

### Scenario 3 — Static website

A company needs to host a static website with low cost and high scalability.

**Solution:** Amazon S3 can host the static content, with CloudFront used for global content delivery.

## AWS Interview Questions

1. What is an Availability Zone?
2. What is the difference between a Security Group and NACL?
3. What is the difference between public and private subnets?
4. Why is a NAT Gateway used?
5. What is an IAM Role?
6. What is the difference between Multi-AZ and Read Replica?
7. What is an AMI?
8. What happens when an EC2 instance is stopped?
9. What is Auto Scaling?
10. What is the difference between SNS and SQS?
