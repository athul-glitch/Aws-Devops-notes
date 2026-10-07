# Amazon EC2

## What is Amazon EC2?

Amazon Elastic Compute Cloud (EC2) provides resizable virtual servers called instances in the AWS Cloud.

EC2 is commonly used to run:

* Websites
* Web applications
* APIs
* Backend applications
* Linux and Windows servers
* Application workloads

---

## EC2 Instance

An EC2 instance is a virtual server running in AWS.

When launching an instance, important choices include:

* AMI
* Instance type
* Key pair
* VPC
* Subnet
* Security group
* Storage
* IAM role
* User data

---

## AMI

AMI stands for Amazon Machine Image.

An AMI contains the information required to launch an EC2 instance.

Examples:

* Amazon Linux
* Ubuntu
* Windows Server

A custom AMI can also be created from an existing EC2 instance.

---

## Instance Types

EC2 instance types are optimized for different workloads.

### General Purpose

Balanced compute, memory, and networking.

Examples:

* t3.micro
* t3.small
* t3.medium
* m7i.large

### Compute Optimized

Designed for workloads that require higher CPU performance.

Example:

* c7i.large

### Memory Optimized

Designed for applications that require large amounts of memory.

Example:

* r7i.large

### Storage Optimized

Designed for workloads that require high-speed local storage.

Example:

* i7i.large

---

## Instance Lifecycle

Common EC2 states:

* Pending
* Running
* Stopping
* Stopped
* Shutting-down
* Terminated

### Stop

The instance is shut down but can usually be started again.

EBS volumes normally remain available.

### Terminate

The instance is permanently deleted.

The root EBS volume is normally deleted if its `DeleteOnTermination` setting is enabled.

---

## Key Pair

A key pair is used to securely connect to an EC2 instance.

For Linux instances, an SSH private key is commonly used.

Example:

```bash
ssh -i key.pem ec2-user@PUBLIC-IP
```

The private key should be kept secure and should not be uploaded to GitHub.

---

## Security Groups

A Security Group acts as a virtual firewall for an EC2 instance.

It controls:

* Inbound traffic
* Outbound traffic

Example:

```text
Inbound Rules

SSH     TCP 22    → My IP
HTTP    TCP 80    → 0.0.0.0/0
HTTPS   TCP 443   → 0.0.0.0/0
```

Security Groups are stateful.

If an incoming connection is allowed, the response traffic is automatically allowed.

---

## EBS

Amazon Elastic Block Store (EBS) provides persistent block storage for EC2 instances.

Common uses:

* Operating system disk
* Application files
* Database storage
* Persistent data

Common EBS volume types include:

* gp3
* io2
* st1
* sc1

### EBS and EC2

```text
EC2 Instance
     |
     |
   EBS Volume
     |
Persistent Storage
```

EBS volumes generally persist independently of the instance when configured appropriately.

---

## Elastic IP

An Elastic IP is a static public IPv4 address that can be associated with an EC2 instance or network interface.

It can be useful when an application requires a stable public IP address.

Example:

```text
Elastic IP
    |
EC2 Instance
```

For many modern architectures, DNS through Route 53 is preferred instead of relying directly on a fixed public IP.

---

## User Data

EC2 User Data allows commands or scripts to run during the initial instance launch.

Example:

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd
systemctl enable httpd
systemctl start httpd

echo "Hello from EC2" > /var/www/html/index.html
```

User Data is commonly used to automate initial server configuration.

---

## IAM Role for EC2

An IAM role can be attached to an EC2 instance to provide AWS permissions.

Example:

```text
EC2
 |
IAM Role
 |
IAM Policy
 |
AWS Services
```

