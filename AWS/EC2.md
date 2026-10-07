# Amazon EC2

## What is Amazon EC2?

Amazon Elastic Compute Cloud (EC2) provides resizable virtual servers
called instances in the AWS Cloud.

EC2 is commonly used to run:

- Websites
- Web applications
- APIs
- Backend applications
- Linux and Windows servers
- Application workloads

## EC2 Instance

An EC2 instance is a virtual server running in AWS.

When launching an instance, important choices include:

- AMI
- Instance type
- Key pair
- VPC
- Subnet
- Security group
- Storage
- IAM role
- User data

## AMI

AMI stands for Amazon Machine Image.

An AMI contains the information required to launch an EC2 instance.

Examples:

- Amazon Linux
- Ubuntu
- Windows Server

A custom AMI can also be created from an existing EC2 instance.

## Instance Types

EC2 instance types are optimized for different workloads.

### General Purpose

Balanced compute, memory, and networking.

Examples:

- t3.micro
- t3.small
- t3.medium
- m7i.large

### Compute Optimized

Designed for workloads that require higher CPU performance.

Example:

- c7i.large

### Memory Optimized

Designed for applications that require large amounts of memory.

Example:

- r7i.large

## Instance Lifecycle

Common EC2 states:

- Pending
- Running
- Stopping
- Stopped
- Shutting-down
- Terminated

### Stop

The instance is shut down but can usually be started again.

EBS volumes remain available.

### Terminate

The instance is permanently deleted.

The root EBS volume is normally deleted if its
`DeleteOnTermination` setting is enabled.

## Key Pair

A key pair is used to securely connect to an EC2 instance.

For Linux instances, an SSH private key is commonly used.

Example:

```bash
ssh -i key.pem ec2-user@PUBLIC-IP

