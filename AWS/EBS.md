# Amazon Elastic Block Store (EBS)

## What is Amazon EBS?

Amazon Elastic Block Store (**EBS**) is a managed **block storage service for Amazon EC2**.

An EBS volume acts like a virtual hard disk that can be attached to an EC2 instance.

```text
EC2 Instance
     |
     ↓
  EBS Volume
     |
     ↓
Operating System / Application Data
```

EBS is commonly used for:

* Operating system disks
* Application files
* Databases
* Persistent application data

---

# Why Use EBS?

EC2 instances need storage for the operating system and applications.

EBS provides **persistent block storage** that can exist independently of the lifecycle of an EC2 instance.

Example:

```text
EC2
 |
 └── EBS
      |
      └── Application Data
```

If the EC2 instance is stopped, the EBS volume can continue to exist.

---

# EBS vs Instance Store

EC2 can use two major types of local storage:

### EBS

Persistent block storage.

```text
EC2
 ↓
EBS
 ↓
Persistent Data
```

### Instance Store

Temporary local storage physically associated with the host.

```text
EC2 Host
   ↓
Instance Store
   ↓
Temporary Data
```

Instance store data can be lost when the instance is stopped, terminated, or otherwise moved depending on the instance lifecycle.

For important persistent data, EBS is generally preferred.

---

# EBS Volume

An EBS volume is a block storage device.

Example:

```text
EBS Volume
Size: 30 GB
Type: gp3
```

It can be attached to a compatible EC2 instance.

Conceptually:

```text
EC2
 |
 ├── Root EBS Volume
 |
 └── Additional EBS Volume
```

---

# Root Volume

The **root volume** contains the operating system of the EC2 instance.

Example:

```text
EC2
 |
 └── /dev/root
       |
       └── Operating System
```

When launching an EC2 instance from an AMI, the root EBS volume is normally created from the AMI configuration.

---

# Additional EBS Volume

You can attach additional EBS volumes for application data.

Example:

```text
EC2
 |
 ├── Root Volume → OS
 |
 └── Data Volume → Application Data
```

This can separate operating-system storage from application data.

---

# EBS Volume Types

Important EBS volume families include:

* General Purpose SSD
* Provisioned IOPS SSD
* Throughput Optimized HDD
* Cold HDD

The most commonly encountered types for general workloads are **gp3** and **io2**.

---

# General Purpose SSD (gp3)

`gp3` is a general-purpose SSD volume type.

It is suitable for many workloads such as:

* Boot volumes
* Application servers
* Development environments
* General databases

Conceptually:

```text
General Workload
      ↓
     gp3
      ↓
   EBS Volume
```

One advantage of gp3 is that storage size, IOPS and throughput can be configured independently within supported limits.

---

# Provisioned IOPS SSD (io2)

`io2` is designed for workloads requiring high and consistent I/O performance.

Typical use cases include:

* Critical databases
* High-performance transactional workloads
* I/O-intensive applications

Think:

```text
High / Consistent IOPS
        ↓
       io2
```

---

# Throughput Optimized HDD (st1)

`st1` is designed for **throughput-intensive workloads**.

Examples:

* Big data
* Data warehouses
* Log processing
* Large sequential workloads

Think:

```text
High Sequential Throughput
          ↓
         st1
```

It is not designed for workloads requiring high random IOPS.

---

# Cold HDD (sc1)

`sc1` is designed for **infrequently accessed, throughput-oriented data**.

It is useful when low cost is more important than high performance.

Think:

```text
Infrequent Access
       +
Low Cost
       ↓
      sc1
```

---

# EBS Volume Selection

A simple way to remember the common choices:

```text
General Purpose
      ↓
     gp3

High / Consistent IOPS
      ↓
     io2

High Sequential Throughput
      ↓
     st1

Infrequent Access + Low Cost
      ↓
     sc1
```

---

# EBS Availability Zone

An EBS volume is associated with a specific **Availability Zone**.

For example:

```text
ap-south-1a
     |
     └── EBS Volume
```

You normally attach the EBS volume to an EC2 instance in the same Availability Zone.

Example:

```text
ap-south-1a
 |
 ├── EC2
 └── EBS
```

You cannot simply attach a normal EBS volume from one Availability Zone directly to an EC2 instance in another Availability Zone.

---

# EBS Multi-Attach

Certain EBS volume types support **Multi-Attach**, allowing a supported volume to be attached to multiple compatible EC2 instances within the same Availability Zone.

Conceptually:

```text
        EBS Volume
        /        \
       ↓          ↓
     EC2 A      EC2 B
```

This is a specialized feature and requires an application that can safely coordinate concurrent access.

---

# EBS Snapshots

An **EBS snapshot** is a point-in-time backup of an EBS volume.

```text
EBS Volume
     |
     ↓
 Snapshot
     |
     ↓
 Backup
```

Snapshots are useful for:

* Backup
* Disaster recovery
* Creating new volumes
* Creating AMIs
* Migrating data

---

# Snapshot Storage

EBS snapshots are stored in AWS-managed infrastructure rather than remaining attached to the EC2 instance.

This allows a volume to be backed up independently of the instance.

Example:

```text
EC2
 |
EBS Volume
 |
 ↓
EBS Snapshot
```

---

# Creating a New Volume from Snapshot

A snapshot can be used to create a new EBS volume.

```text
Snapshot
   |
   ↓
New EBS Volume
   |
   ↓
EC2
```

This is useful when restoring data or creating additional environments.

---

# EBS Snapshots and Incremental Backups

EBS snapshots are incremental after the first snapshot.

Conceptually:

```text
Snapshot 1
   ↓
Full initial snapshot

Snapshot 2
   ↓
Changed blocks

Snapshot 3
   ↓
Additional changed blocks
```

AWS manages the underlying snapshot storage.

---

# EBS Encryption

EBS supports encryption using AWS Key Management Service (**KMS**).

Encryption can protect:

* Data at rest
* Volume data
* Snapshots
* Data copied from encrypted volumes

Conceptually:

```text
EC2
 |
Encrypted EBS
 |
KMS
```

---

# Encrypted EBS Workflow

When creating an encrypted volume:

```text
EBS Volume
     ↓
KMS Key
     ↓
Encrypted Storage
```

Encryption can be enabled when creating a new volume.

---

# EBS Encryption and Snapshots

If an EBS volume is encrypted, snapshots created from it are also encrypted.

Conceptually:

```text
Encrypted EBS
      ↓
Encrypted Snapshot
      ↓
Encrypted New Volume
```

---

# EBS Encryption by Default

AWS accounts can enable **EBS encryption by default** for a Region.

This helps ensure newly created EBS volumes are encrypted without requiring users to manually enable encryption every time.

This is a useful security best practice.

---

# EBS and KMS

KMS manages the cryptographic keys used for EBS encryption.

Example:

```text
EC2
 ↓
Encrypted EBS
 ↓
AWS KMS
```

IAM permissions and KMS key policies determine who can use the relevant encryption keys.

---

# EBS Performance

EBS performance can depend on:

* Volume type
* Volume size
* Provisioned IOPS
* Throughput
* EC2 instance capabilities

For example:

```text
gp3
 ↓
Configure:
- Size
- IOPS
- Throughput
```

For demanding workloads, choosing the correct volume type is important.

---

# EBS and EC2

EBS is tightly integrated with EC2.

Typical architecture:

```text
             EC2
              |
       ┌──────┴──────┐
       ↓             ↓
   Root EBS      Data EBS
       ↓             ↓
       OS        Application Data
```

---

# EBS and Auto Scaling

When using an Auto Scaling Group, each EC2 instance normally gets its own configured storage based on the launch template or launch configuration.

Example:

```text
Auto Scaling Group
      |
   ┌──┼──┐
   ↓  ↓  ↓
 EC2 EC2 EC2
  |   |   |
 EBS EBS EBS
```

Do not assume that one normal EBS volume is automatically shared across all instances.

For shared file storage, consider services such as **EFS**.

---

# EBS vs EFS

This is an important SAA comparison.

### EBS

Block storage.

```text
EC2
 ↓
EBS
```

Best suited for:

* OS disks
* Databases
* Single-instance application storage
* Block-level workloads

### EFS

Managed shared file storage.

```text
EC2 A ─┐
EC2 B ─┼── EFS
EC2 C ─┘
```

Best suited when multiple EC2 instances need shared file access.

Simple rule:

```text
EBS → Block storage

EFS → Shared file storage
```

---

# EBS vs S3

### EBS

Block storage attached to EC2.

```text
EC2
 ↓
EBS
```

### S3

Object storage accessed through APIs.

```text
Application
    ↓
S3
    ↓
Objects
```

Use EBS when an application requires a disk-like block device.

Use S3 when storing objects such as:

* Images
* Videos
* Backups
* Documents
* Static files

---

# EBS and AMI

An AMI can contain information about the EBS volumes required to launch an EC2 instance.

Conceptually:

```text
AMI
 ↓
EC2 Launch
 ↓
EBS Root Volume
```

You can also create an AMI from an EC2 instance for repeatable deployments.

---

# EBS and Terraform

Terraform can manage EBS resources.

Common resources include:

```text
aws_ebs_volume
aws_volume_attachment
```

Conceptually:

```text
Terraform
    ↓
EBS Volume
    ↓
Attach to EC2
```

For many EC2 launch configurations, EBS settings can also be defined directly in the EC2 resource or launch template.

---

# EBS and Sheepeye

Your Sheepeye EC2 instance uses EBS for its operating-system storage.

Conceptually:

```text
Sheepeye EC2
      |
      ↓
    EBS
      |
      ├── Linux OS
      ├── Application
      └── Application files
```

For your Terraform infrastructure, EBS can be configured through the EC2 launch configuration.

You don't need to create a complicated separate EBS architecture for Sheepeye right now.

---

# Backup Strategy

A simple EBS backup architecture can use snapshots:

```text
EBS Volume
    ↓
Snapshot
    ↓
Backup
```

For larger environments, AWS Backup can also be used to centrally manage backups across supported AWS resources.

---

# Common SAA Scenarios

## Scenario 1: EC2 needs persistent disk storage

> An EC2 application needs a persistent block device.

Use:

```text
EBS
```

---

## Scenario 2: Multiple EC2 instances need shared files

> Several EC2 instances need simultaneous access to the same file system.

Use:

```text
EFS
```

rather than a normal EBS volume.

---

## Scenario 3: Database needs high IOPS

> A critical database requires high and consistent I/O performance.

Consider:

```text
io2
```

---

## Scenario 4: General-purpose application server

> An application server needs standard SSD block storage.

Consider:

```text
gp3
```

---

## Scenario 5: Large sequential workload

> An application performs large sequential reads and writes and needs high throughput.

Consider:

```text
st1
```

---

## Scenario 6: Low-cost infrequently accessed data

> Data is accessed infrequently and low storage cost is important.

Consider:

```text
sc1
```

---

## Scenario 7: Back up an EC2 disk

> An organization needs a point-in-time backup of an EBS volume.

Use:

```text
EBS Snapshot
```

---

## Scenario 8: Protect data at rest

> A company requires EC2 disk data to be encrypted.

Use:

```text
Encrypted EBS
+
KMS
```

---

## Scenario 9: EBS volume in another Availability Zone

> An EBS volume is in `ap-south-1a`, but the EC2 instance is in `ap-south-1b`.

A normal EBS volume cannot simply be attached across Availability Zones.

A common approach is:

```text
EBS Snapshot
      ↓
Create Volume
      ↓
Target Availability Zone
```

---

# Key Points

* **EBS = block storage for EC2.**
* EBS volumes provide persistent storage.
* A normal EBS volume is associated with a specific Availability Zone.
* EBS is commonly used for EC2 root and data volumes.
* `gp3` is a common general-purpose SSD choice.
* `io2` is designed for high and consistent IOPS workloads.
* `st1` is designed for throughput-intensive sequential workloads.
* `sc1` is designed for infrequently accessed, low-cost HDD storage.
* EBS snapshots provide point-in-time backups.
* Snapshots are incremental after the initial snapshot.
* EBS supports encryption using KMS.
* EBS encryption can be enabled by default for a Region.
* **EBS = block storage.**
* **EFS = shared file storage.**
* **S3 = object storage.**
* For SAA, choose EBS when the requirement is **persistent block storage attached to EC2**.
