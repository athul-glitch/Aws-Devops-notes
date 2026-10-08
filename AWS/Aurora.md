# Amazon Aurora

## What is Amazon Aurora?

Amazon Aurora is a **managed relational database engine** provided by AWS.

It is compatible with:

* MySQL
* PostgreSQL

Aurora is designed to provide higher performance, availability, and scalability than standard managed database engines in many workloads.

Aurora is part of **Amazon RDS**, so AWS manages tasks such as:

* Database provisioning
* Patching
* Backups
* Monitoring
* Failover
* Infrastructure maintenance

---

## Why Aurora Matters

Aurora is useful when an application needs:

* A relational SQL database
* High availability
* Automatic failover
* Read scaling
* Managed backups
* High performance
* AWS-managed database infrastructure

For example:

```text
Application
     |
     v
Aurora Database
     |
     +---- Primary Instance
     |
     +---- Aurora Replicas
```

---

# Aurora vs RDS

Aurora is an RDS database engine, while RDS also supports engines such as:

* MySQL
* PostgreSQL
* MariaDB
* Oracle
* SQL Server

The important difference is that Aurora uses an AWS-designed database architecture with a distributed storage layer.

### Simple comparison

| Feature                          | RDS MySQL/PostgreSQL | Aurora |
| -------------------------------- | -------------------- | ------ |
| Managed by AWS                   | Yes                  | Yes    |
| SQL database                     | Yes                  | Yes    |
| Automatic backups                | Yes                  | Yes    |
| Multi-AZ                         | Yes                  | Yes    |
| Read replicas                    | Yes                  | Yes    |
| Distributed storage architecture | No                   | Yes    |
| Aurora Replicas                  | No                   | Yes    |
| MySQL/PostgreSQL compatible      | Yes                  | Yes    |

Aurora is often considered when the workload requires stronger availability, performance, or read scalability.

---

# Aurora Storage

Aurora separates **compute** from **database storage**.

The database instances connect to a distributed Aurora storage system.

```text
        Aurora Cluster
              |
      +-------+-------+
      |               |
 Primary Instance   Replica
      |               |
      +-------+-------+
              |
       Shared Storage
```

Aurora storage automatically grows as the database needs more space.

This is different from attaching a traditional EBS volume directly to a database instance.

---

# Aurora Cluster

An Aurora cluster can contain:

* One primary DB instance
* Zero or more Aurora Replicas

Example:

```text
              Aurora Cluster
                    |
          +---------+---------+
          |                   |
      Primary             Replica
      (Writer)             (Reader)
          |
      Read/Write
```

The **primary instance** handles write operations.

Aurora Replicas can handle read operations.

---

# Aurora Primary Instance

The primary instance is responsible for:

* Write operations
* Read operations
* Database administration operations

Example:

```text
Application
     |
     v
Primary
     |
 Read + Write
```

If the primary instance fails, Aurora can perform an automatic failover to an available replica.

---

# Aurora Replicas

Aurora Replicas are additional database instances in the same Aurora cluster.

They are mainly used for:

* Read scaling
* High availability
* Failover

Example:

```text
             Aurora Cluster
                   |
          +--------+--------+
          |        |        |
       Primary  Replica  Replica
        Writer   Reader    Reader
```

Applications with many read requests can distribute those requests across replicas.

---

# Aurora Reader Endpoint

The **Reader Endpoint** is used to connect applications to Aurora replicas.

Example:

```text
Application
     |
     v
Reader Endpoint
     |
 +---+---+
 |       |
 v       v
Replica Replica
```

It helps distribute read traffic among available Aurora Replicas.

Use it when the application needs to scale read operations.

---

# Aurora Writer Endpoint

The **Writer Endpoint** connects applications to the current primary instance.

Example:

```text
Application
     |
     v
Writer Endpoint
     |
     v
Primary Instance
```

The writer endpoint automatically points to the current primary after a failover.

This means applications do not normally need to know the individual database instance address.

---

# Aurora Failover

Aurora supports automatic failover.

Suppose the primary instance fails:

```text
Before:

Primary
   |
   X  Failure

Replica
```

Aurora can promote a replica:

```text
After:

New Primary
   |
   +---- Former Replica
```

The application can continue using the cluster endpoint.

### Why this matters

This improves:

* Availability
* Recovery
* Application resilience

---

# Aurora Multi-AZ

Aurora is designed for high availability across multiple Availability Zones.

The Aurora storage system maintains multiple copies of data across Availability Zones.

Example:

```text
Region
|
+-- AZ-A
|    |
|   Primary
|
+-- AZ-B
|    |
|   Replica
|
+-- AZ-C
     |
    Replica
```

This protects the database from an Availability Zone failure.

---

# Aurora Read Scaling

If an application receives a large number of read requests, additional Aurora Replicas can be added.

Example:

```text
             Application
                  |
          +-------+-------+
          |               |
        Writes           Reads
          |               |
       Primary       Reader Endpoint
                          |
                  +-------+-------+
                  |       |       |
               Replica Replica Replica
```

This separates read workload from write workload.

---

# Aurora Auto Scaling

Aurora can automatically adjust the number of Aurora Replicas using **Aurora Auto Scaling**.

For example:

```text
High read traffic
       |
       v
More Replicas

Low read traffic
       |
       v
Fewer Replicas
```

This can help handle changing read workloads.

---

# Aurora Backups

Aurora provides automated backups.

Aurora backups can be used for:

* Point-in-time recovery
* Database restoration
* Disaster recovery

Aurora also supports manual database snapshots.

### Snapshot

A snapshot is a point-in-time backup that can be retained for later restoration.

---

# Aurora Global Database

Aurora Global Database is designed for applications that need:

* Cross-region disaster recovery
* Global applications
* Lower read latency in different regions

Example:

```text
Primary Region
     |
     | Replication
     v
Secondary Region
     |
     v
Read workload
```

This can provide geographically distributed database access.

---

# Aurora Security

Aurora integrates with AWS security services.

Important security features include:

* IAM
* Security Groups
* KMS encryption
* TLS/SSL
* VPC
* Secrets Manager

Example:

```text
Application
     |
 Security Group
     |
     v
Aurora
     |
    KMS
 Encryption
```

Aurora databases can be encrypted using **AWS KMS**.

---

# Aurora Networking

Aurora databases run inside an Amazon VPC.

They are normally placed in private subnets.

Example:

```text
VPC
|
+-- Public Subnet
|      |
|     ALB
|
+-- Private Subnet
       |
      Aurora
```

Applications communicate with Aurora through the VPC network.

Aurora does not normally need direct public internet access.

---

# Aurora Security Groups

Aurora uses security groups to control network access.

Example:

```text
EC2 Security Group
        |
        | TCP 5432
        v
Aurora PostgreSQL
```

For PostgreSQL:

```text
TCP 5432
```

For MySQL-compatible Aurora:

```text
TCP 3306
```

A common best practice is to allow database access only from the application's security group.

Avoid:

```text
0.0.0.0/0
```

for database ports.

---

# Aurora Monitoring

Aurora integrates with:

* Amazon CloudWatch
* Enhanced Monitoring
* Performance Insights

CloudWatch can monitor metrics such as:

* CPU utilization
* Database connections
* Read operations
* Write operations
* Storage-related metrics

Monitoring helps identify:

* High CPU
* Connection problems
* Heavy database workload
* Performance bottlenecks

---

# Aurora and IAM

IAM controls access to AWS resources.

Aurora can also support **IAM database authentication** for compatible configurations.

However, IAM permissions and database permissions are separate concepts.

For example:

```text
IAM
 |
 +-- Controls AWS API access

Database Users
 |
 +-- Controls database-level access
```

Understanding this distinction is important when troubleshooting access problems.

---

# Aurora vs DynamoDB

Aurora and DynamoDB solve different problems.

| Feature          | Aurora                      | DynamoDB                                |
| ---------------- | --------------------------- | --------------------------------------- |
| Database type    | Relational                  | NoSQL                                   |
| SQL              | Yes                         | No traditional SQL                      |
| Tables/relations | Yes                         | No traditional relational model         |
| Transactions     | Yes                         | Yes                                     |
| Scaling model    | Relational database scaling | Serverless NoSQL scaling                |
| Best for         | SQL applications            | High-scale key-value/document workloads |

Choose Aurora when the application needs a relational SQL database.

Choose DynamoDB when the application is designed around a NoSQL key-value/document model.

---

# Aurora vs S3

Aurora is a database.

S3 is object storage.

```text
Aurora
→ Structured application data

S3
→ Files, images, videos, backups, objects
```

For example, an application could store:

```text
Customer information → Aurora

Uploaded images → S3
```

---

# Aurora vs EBS

EBS provides block storage for EC2.

Aurora provides a managed relational database service with its own distributed storage architecture.

```text
EC2 + EBS
    |
Application manages database software

Aurora
    |
AWS manages database infrastructure
```

Use Aurora when you want AWS to manage the database infrastructure.

---

# Aurora with Application Architecture

A typical AWS architecture could look like:

```text
                  Internet
                     |
                     v
                Route 53
                     |
                     v
                CloudFront
                     |
                     v
                   ALB
                     |
              +------+------+
              |             |
             EC2           EC2
              |             |
              +------+------+
                     |
                     v
                  Aurora
```

The application servers communicate with Aurora through private networking.

---

# Aurora in a Three-Tier Archi
