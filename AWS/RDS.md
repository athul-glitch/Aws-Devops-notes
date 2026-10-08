# Amazon RDS

## What is Amazon RDS?

Amazon Relational Database Service (RDS) is a managed service for running relational databases in AWS.

RDS handles many administrative tasks such as:

* Database provisioning
* Backups
* Software patching
* Monitoring
* High availability options
* Storage management

RDS can be used for applications that need a traditional relational database.

---

## Supported Database Engines

RDS supports several relational database engines, including:

* Amazon Aurora
* MySQL
* PostgreSQL
* MariaDB
* Oracle
* Microsoft SQL Server

The application connects to the database using the appropriate database engine and port.

Examples:

```text
MySQL       → TCP 3306
PostgreSQL  → TCP 5432
```

---

## RDS Architecture

A simple application architecture can look like:

```text
User
  |
  ↓
Application / EC2
  |
  ↓
RDS Database
```

The application communicates with RDS over the network.

RDS can be placed in private subnets so that the database is not directly accessible from the public internet.

---

## DB Instance

An RDS DB instance provides the compute and memory resources required to run the database.

When creating an RDS database, important choices include:

* Database engine
* DB instance class
* Storage
* VPC
* Subnet group
* Security group
* Availability Zone
* Encryption
* Backup settings

---

## RDS Storage

RDS uses storage volumes for database data.

Common storage options include:

* General Purpose SSD
* Provisioned IOPS SSD

Storage choice depends on the database workload and performance requirements.

---

## Automated Backups

RDS can automatically create backups of a database.

Automated backups can be used for:

* Point-in-time recovery
* Recovering from accidental changes
* Database protection

The backup retention period can be configured.

---

## Manual Snapshots

RDS also supports manual DB snapshots.

A snapshot is a point-in-time backup of the database.

Snapshots can be useful when:

* Before major changes
* Before deleting a database
* Creating a new database from an existing one
* Long-term backup requirements

---

## Multi-AZ

Multi-AZ is used to improve **high availability**.

A standby database is maintained in another Availability Zone.

Example:

```text
              AWS Region
                  |
        ┌─────────┴─────────┐
        ↓                   ↓
 Availability Zone A   Availability Zone B
        |                   |
   Primary DB          Standby DB
        |
        └──── Synchronous Replication ────┘
```

If the primary database becomes unavailable, RDS can fail over to the standby.

### Important

Multi-AZ is primarily for **high availability and failover**, not for scaling read traffic.

---

## Read Replicas

Read Replicas are used to improve read performance and reduce the load on the primary database.

Example:

```text
              Application
                   |
          ┌────────┴────────┐
          ↓                 ↓
     Primary DB        Read Replica
     (Read/Write)        (Read)
```

Applications can send read-heavy workloads to the read replica.

### Multi-AZ vs Read Replica

| Feature              | Multi-AZ                  | Read Replica         |
| -------------------- | ------------------------- | -------------------- |
| Main purpose         | High availability         | Read scaling         |
| Standby/replica      | Standby                   | Readable replica     |
| Handles read traffic | No                        | Yes                  |
| Failover             | Yes                       | Not primarily        |
| Use case             | Disaster/failure recovery | Read-heavy workloads |

---

## RDS Security Groups

RDS instances can use Security Groups to control network access.

Example:

```text
Application Security Group
          |
      TCP 5432
          |
          ↓
     RDS PostgreSQL
```

A good design is to allow the database port only from the application's Security Group rather than allowing access from the entire internet.

Avoid:

```text
0.0.0.0/0 → TCP 5432
```

for a private production database.

---

## RDS Encryption

RDS supports encryption at rest.

Encryption can protect:

* Database storage
* Automated backups
* Read replicas
* Snapshots

AWS Key Management Service (KMS) can be used for encryption key management.

RDS can also use encrypted connections such as TLS/SSL for data in transit when supported and configured.

---

## RDS Subnet Groups

An RDS DB subnet group defines which subnets can be used by the database.

For high availability, the subnet group normally includes subnets from multiple Availability Zones.

Example:

```text
VPC
 |
 ├── Private Subnet - AZ A
 |
 └── Private Subnet - AZ B
          |
       RDS
```

Databases are commonly placed in private subnets.

---

## RDS Endpoint

Applications connect to RDS using a DNS endpoint rather than directly depending on a fixed IP address.

Example:

```text
Application
     |
     ↓
RDS Endpoint
     |
     ↓
Database
```

The endpoint can remain consistent even when the underlying database infrastructure changes during events such as failover.

---

## RDS vs Database on EC2

### Database on EC2

You manage:

* Operating system
* Database installation
* Patching
* Backups
* Maintenance
* High availability

### RDS

AWS manages much of the underlying database infrastructure.

You mainly manage:

* Database configuration
* Database users
* Schema
* Application data
* Performance settings

For many workloads, RDS reduces operational overhead.

---

## RDS vs Aurora

Amazon Aurora is a relational database engine designed by AWS and compatible with MySQL and PostgreSQL.

Aurora provides AWS-managed database capabilities with a distributed storage architecture.

### RDS

```text
RDS
 |
├── MySQL
├── PostgreSQL
├── MariaDB
├── Oracle
└── SQL Server
```

### Aurora

```text
Aurora
 |
├── Aurora MySQL-Compatible
└── Aurora PostgreSQL-Compatible
```

Aurora is often considered when higher performance, scalability, and AWS-managed database capabilities are required.

---

## RDS Monitoring

RDS integrates with Amazon CloudWatch.

CloudWatch can monitor metrics such as:

* CPU utilization
* Database connections
* Free storage space
* Read/write activity
* Network traffic

Monitoring helps identify performance and capacity problems.

---

## RDS and IAM

IAM can control access to AWS resources related to RDS.

For some database engines and configurations, IAM database authentication can also be used instead of traditional database passwords.

IAM permissions should follow the principle of least privilege.

---

## RDS and Sheepeye

If Sheepeye uses an AWS-managed relational database, a possible architecture is:

```text
Internet
   |
   ↓
Application / EC2
   |
   ↓
Private Subnet
   |
   ↓
RDS PostgreSQL
```

The application Security Group can be allowed to connect to the RDS Security Group on TCP port `5432`.

For the current Sheepeye project, using the existing database can remain the practical choice while learning RDS separately. There is no need to move the production database to RDS just for the sake of adding another AWS service.

---

## Key Points

* RDS is a **managed relational database service**.
* It supports engines such as MySQL, PostgreSQL, MariaDB, Oracle, and SQL Server.
* Aurora is an AWS-designed relational database engine.
* RDS can provide automated backups and snapshots.
* Multi-AZ is mainly for **high availability and failover**.
* Read Replicas are mainly for **read scaling**.
* RDS can be placed in private subnets.
* Security Groups control network access to the database.
* RDS supports encryption using AWS KMS.
* CloudWatch can monitor RDS performance.
* Applications normally connect using an RDS endpoint.
* RDS reduces the operational work required compared with managing a database directly on EC2.
