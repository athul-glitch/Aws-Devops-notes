# Amazon Elastic File System (EFS)

## What is Amazon EFS?

Amazon Elastic File System (**EFS**) is a managed, elastic file system that provides shared file storage for AWS workloads.

Multiple EC2 instances can access the same EFS file system at the same time.

```text id="z5q1g0"
             EFS
              |
       ┌──────┼──────┐
       ↓      ↓      ↓
     EC2-1  EC2-2  EC2-3
```

This makes EFS useful when multiple servers need access to the same files.

---

## Why Use EFS?

Consider an application running on multiple EC2 instances.

If each instance has its own local storage:

```text id="sm3s3h"
EC2-1 → Files A
EC2-2 → Files B
EC2-3 → Files C
```

The files are not automatically shared.

With EFS:

```text id="o6grl6"
EC2-1 ──┐
EC2-2 ──┼──→ EFS
EC2-3 ──┘
```

All instances can access the same file system.

---

## EFS is a File System

EFS provides a traditional file-system interface.

Applications can work with files and directories.

Example:

```text id="s4j4x4"
/app
 ├── uploads
 ├── images
 ├── documents
 └── logs
```

EC2 instances can mount the EFS file system and access these directories.

---

## EFS and NFS

EFS uses the **Network File System (NFS)** protocol.

EC2 instances communicate with EFS over the network.

Conceptually:

```text id="p6m7wv"
EC2
 |
 | NFS
 ↓
EFS
```

For typical EFS usage, the relevant protocol is **NFSv4**.

---

## Regional Service

An EFS file system is designed to be accessed from multiple Availability Zones within an AWS Region.

Example:

```text id="25x5we"
AWS Region
│
├── AZ-A
│    └── EC2
│         \
│          \
│           → EFS
│          /
│         /
└── AZ-B
     └── EC2
```

This makes EFS suitable for applications that run across multiple Availability Zones.

---

## EFS Mount Targets

To allow EC2 instances in a VPC to access EFS, EFS uses **mount targets**.

A mount target provides network connectivity to the EFS file system from a specific Availability Zone.

Example:

```text id="v8v4r1"
VPC
│
├── AZ-A
│   ├── EC2
│   └── EFS Mount Target
│
└── AZ-B
    ├── EC2
    └── EFS Mount Target
```

Applications typically connect to the EFS mount target in their Availability Zone.

---

## Security Groups

EFS uses security groups to control network access to its mount targets.

A common setup is:

```text id="y9f4ed"
EC2 Security Group
        |
        | NFS / TCP 2049
        ↓
EFS Security Group
```

The EFS security group should allow the required NFS traffic from the appropriate EC2 security group.

---

## NFS Port

EFS uses:

```text id="i8ik0u"
TCP 2049
```

for NFS traffic.

This is an important networking detail when troubleshooting EFS connectivity.

---

## EFS Performance Modes

EFS provides different performance options depending on workload requirements.

Common concepts include:

* General Purpose
* Max I/O

### General Purpose

Designed for most workloads and provides low latency.

### Max I/O

Designed for workloads requiring very high levels of aggregate throughput and concurrency, with higher latency than General Purpose.

For most normal applications, General Purpose is the typical choice.

---

## EFS Throughput Modes

EFS also provides different throughput modes.

Common modes include:

* Elastic
* Provisioned
* Bursting

### Elastic Throughput

Automatically scales throughput based on workload requirements.

Useful for workloads with unpredictable traffic patterns.

### Provisioned Throughput

Allows you to configure the desired throughput independently of the amount of data stored.

Useful when an application needs predictable throughput.

### Bursting Throughput

Provides throughput that can scale based on the amount of data stored and available burst capacity.

---

## EFS Storage Classes

EFS supports different storage classes for balancing performance and cost.

Important concepts include:

* EFS Standard
* EFS Infrequent Access (IA)
* EFS Archive

Less frequently accessed files can be moved to lower-cost storage classes using lifecycle policies.

Example:

```text id="bq5k8n"
Frequently accessed
       ↓
EFS Standard

Less frequently accessed
       ↓
EFS IA

Long-term / rarely accessed
       ↓
EFS Archive
```

---

## Lifecycle Management

EFS lifecycle management can automatically move files between storage classes based on access patterns.

Example:

```text id="0w5cgd"
File created
    ↓
Standard
    ↓
Not accessed for configured period
    ↓
EFS IA
```

This can reduce storage costs for files that are rarely accessed.

---

## EFS vs EBS

EFS and EBS are both AWS storage services, but they solve different problems.

| Feature           | EFS                          | EBS                                     |
| ----------------- | ---------------------------- | --------------------------------------- |
| Storage type      | File                         | Block                                   |
| Shared across EC2 | Yes                          | Generally attached to one EC2 at a time |
| Access            | Network file system          | Block device                            |
| Scaling           | Elastic                      | Volume-based                            |
| Multi-AZ access   | Designed for regional access | Volume is AZ-specific                   |
| Common use        | Shared files                 | EC2 disks                               |

Simple rule:

```text id="2w8h9n"
Need shared files?
        ↓
       EFS

Need an EC2 disk?
        ↓
       EBS
```

---

## EFS vs S3

EFS and S3 are also different.

| Feature              | EFS                      | S3                             |
| -------------------- | ------------------------ | ------------------------------ |
| Storage model        | File system              | Object storage                 |
| Mount as file system | Yes                      | Not natively like EFS          |
| Shared file access   | Yes                      | Object-based                   |
| Typical use          | Shared application files | Objects, backups, static files |
| Access method        | NFS                      | S3 API                         |

Example:

```text id="j85db7"
Application shared filesystem
        ↓
       EFS

Images / backups / objects
        ↓
        S3
```

---

## EFS and Auto Scaling

EFS is particularly useful with Auto Scaling because new EC2 instances can mount the same file system.

Example:

```text id="at3qjy"
              EFS
               |
       ┌───────┼───────┐
       ↓       ↓       ↓
     EC2-1   EC2-2   EC2-3
       ↑       ↑       ↑
       └── Auto Scaling ──┘
```

When Auto Scaling launches a new instance, the instance can mount EFS and access the same shared files.

This avoids storing important shared application files only on one EC2 instance.

---

## EFS and Load Balancer

A common architecture is:

```text id="mxk6go"
Users
  |
  ↓
ALB
  |
  ↓
Target Group
  |
 ┌┴─────────┐
 ↓          ↓
EC2        EC2
 \          /
  \        /
    ↓    ↓
      EFS
```

The ALB distributes requests.

The EC2 instances process the requests.

EFS provides shared file storage.

---

## EFS Encryption

EFS supports encryption at rest and encryption in transit.

Encryption at rest can use AWS KMS.

```text id="50wzws"
EFS
 |
 ↓
KMS
 |
 ↓
Encryption at Rest
```

For data in transit, EFS supports encrypted network communication using TLS.

---

## EFS Access Points

EFS Access Points provide application-specific entry points into an EFS file system.

They can help control:

* Root directory
* POSIX user/group identity
* Application access

Example:

```text id="3v0x5q"
EFS
 |
 ├── Application A Access Point
 |
 └── Application B Access Point
```

This can make shared EFS storage easier to manage securely for multiple applications.

---

## EFS and POSIX Permissions

EFS follows normal Linux/POSIX file permissions.

For example:

```text id="e7h2fd"
-rw-r--r--  file.txt
```

Ownership and permissions affect which users and processes can access files.

Therefore, troubleshooting EFS access may require checking:

* Linux user
* Group
* File ownership
* File permissions
* EFS security group
* Network connectivity

---

## Mounting EFS on Linux

An EC2 Linux instance can mount EFS as a file system.

Conceptually:

```text id="c5v2q2"
EC2
 |
 ↓
Mount EFS
 |
 ↓
/mnt/efs
```

After mounting, applications can use the EFS directory like a normal file system.

---

## Common EFS Troubleshooting

If an EC2 instance cannot access EFS, check:

### 1. Security Groups

Verify NFS traffic is allowed:

```text id="5x8j5j"
TCP 2049
```

### 2. Mount Target

Check that the appropriate EFS mount target exists and is reachable.

### 3. VPC Networking

Verify:

* Subnet
* Route configuration
* Network connectivity

### 4. Linux Permissions

Check:

* User
* Group
* Ownership
* File permissions

### 5. DNS

EFS mounting normally relies on DNS resolution for the EFS mount target.

---

## EFS and Sheepeye

EFS is **not currently necessary** for your single-EC2 Sheepeye setup.

If Sheepeye later uses multiple EC2 instances behind an ALB, EFS could be useful if those instances need shared files.

Example future architecture:

```text id="qz5kn7"
Route 53
    |
    ↓
CloudFront
    |
    ↓
ALB
    |
    ↓
Auto Scaling Group
    |
 ┌──┴──────────┐
 ↓             ↓
EC2           EC2
 \             /
  \           /
      EFS
```

For your current project, you should **not add EFS just for the sake of adding another AWS service**.

Understanding when it is useful is more important.

---

## EFS in SAA Scenarios

EFS is commonly the correct choice when a question describes:

* Multiple EC2 instances
* Shared file storage
* Linux applications
* Dynamic storage requirements
* Multi-AZ access
* Auto Scaling applications requiring shared files

### Example

> A web application runs on multiple EC2 instances behind an Application Load Balancer. All instances need access to the same uploaded files.

A suitable solution is:

```text id="j2g0j4"
ALB
 |
 ├── EC2
 ├── EC2
 └── EC2
      |
      ↓
     EFS
```

---

## EFS vs EBS vs S3

A useful SAA decision guide:

```text id="3slj8u"
Need a block disk for EC2?
        ↓
       EBS

Need a shared Linux file system?
        ↓
       EFS

Need object storage?
        ↓
        S3
```

This distinction is one of the most important things to remember.

---

## Key Points

* EFS is a managed, elastic **file system**.
* Multiple EC2 instances can access the same EFS file system.
* EFS uses NFS.
* NFS traffic uses **TCP 2049**.
* EFS is designed for access across multiple Availability Zones in a Region.
* Mount targets provide network access to the file system.
* Security Groups control network access to EFS mount targets.
* EFS supports encryption at rest using KMS.
* EFS supports encryption in transit.
* EFS can work well with ALB and Auto Scaling architectures.
* EFS supports lifecycle management and different storage classes.
* **EFS = shared file storage.**
* **EBS = block storage for EC2.**
* **S3 = object storage.**
* For Sheepeye, EFS is not necessary while running a single EC2 instance.
