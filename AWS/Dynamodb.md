# Amazon DynamoDB

## What is Amazon DynamoDB?

Amazon DynamoDB is a **fully managed, serverless NoSQL database** provided by AWS.

It is designed for applications that need:

* Very fast performance
* Low-latency access
* Automatic scaling
* High availability
* Large-scale workloads

Unlike traditional relational databases such as RDS, DynamoDB stores data using a **NoSQL key-value and document model**.

```text
Application
     |
     ↓
DynamoDB
     |
     ↓
NoSQL Data
```

---

## Why Use DynamoDB?

DynamoDB is useful when an application needs predictable, low-latency database access at large scale.

Example:

```text
Users
  |
  ↓
Application
  |
  ↓
DynamoDB
```

AWS manages the underlying infrastructure, so you do not manage:

* Database servers
* Operating systems
* Database installation
* Traditional database patching
* Hardware

---

# DynamoDB Data Model

DynamoDB organizes data into:

```text
Table
  ↓
Items
  ↓
Attributes
```

### Table

A table is similar to a database table in a relational database.

Example:

```text
Customers
```

### Item

An item is similar to a row.

Example:

```json
{
  "customerId": "C101",
  "name": "Rahul",
  "city": "Kochi"
}
```

### Attribute

An attribute is similar to a column, but DynamoDB does not require every item to have the exact same set of attributes.

Example:

```text
customerId
name
city
phone
```

---

# NoSQL vs Relational Database

Traditional relational database:

```text
Table
 ├── Row
 ├── Row
 └── Row
```

DynamoDB:

```text
Table
 ├── Item
 ├── Item
 └── Item
```

DynamoDB is schema-flexible.

Different items can contain different non-key attributes.

---

# Primary Key

Every DynamoDB table must have a primary key.

The primary key uniquely identifies items.

DynamoDB supports two main primary key designs:

1. Partition key
2. Composite primary key

---

## Partition Key

A partition key is a single attribute used to identify an item.

Example:

```text
CustomerID
```

Table:

| CustomerID | Name  | City      |
| ---------- | ----- | --------- |
| C101       | Rahul | Kochi     |
| C102       | Arun  | Bangalore |
| C103       | Amal  | Chennai   |

Here:

```text
Partition Key = CustomerID
```

The partition key determines how DynamoDB distributes data.

---

# Composite Primary Key

A composite primary key contains:

* Partition key
* Sort key

Example:

```text
Partition Key = CustomerID
Sort Key      = OrderID
```

Example:

| CustomerID | OrderID | Amount |
| ---------- | ------- | -----: |
| C101       | O001    |    500 |
| C101       | O002    |    800 |
| C102       | O003    |    300 |

This allows multiple related items to share the same partition key while being differentiated by the sort key.

---

# Sort Key

The sort key is used to organize items that share the same partition key.

Example:

```text
CustomerID = C101

OrderID
O001
O002
O003
```

The combination of:

```text
Partition Key + Sort Key
```

uniquely identifies an item.

---

# Query vs Scan

This is an important DynamoDB concept.

## Query

A Query retrieves items based on their key.

Example:

```text
CustomerID = C101
```

It is generally much more efficient than scanning the entire table.

```text
Query
 ↓
Specific partition
 ↓
Matching items
```

## Scan

A Scan examines items across the table.

```text
Scan
 ↓
Entire table
 ↓
Filter results
```

Scans can become expensive and inefficient for large tables.

### Simple rule

```text
Need specific items?
       ↓
      Query

Need to examine the whole table?
       ↓
      Scan
```

---

# Provisioned Capacity

With **provisioned capacity**, you specify the expected read and write capacity for the table.

Two important concepts are:

* Read Capacity Units (RCU)
* Write Capacity Units (WCU)

AWS uses these to measure DynamoDB throughput.

---

# On-Demand Capacity

With **on-demand capacity**, DynamoDB automatically handles capacity based on application traffic.

You pay based on requests rather than manually provisioning capacity.

This can be useful for:

* Unpredictable workloads
* Variable traffic
* Applications where capacity planning is difficult

---

# Provisioned vs On-Demand

| Feature             | Provisioned                          | On-Demand               |
| ------------------- | ------------------------------------ | ----------------------- |
| Capacity management | You specify capacity                 | AWS manages capacity    |
| Best for            | Predictable workloads                | Unpredictable workloads |
| Scaling             | Configured/automatic scaling options | Automatic               |
| Planning            | Requires capacity planning           | Less capacity planning  |

Simple rule:

```text
Predictable traffic
      ↓
Provisioned

Unpredictable traffic
      ↓
On-Demand
```

---

# DynamoDB Auto Scaling

For provisioned capacity, DynamoDB can automatically adjust capacity based on workload.

Example:

```text
Traffic increases
      ↓
Auto Scaling
      ↓
More capacity

Traffic decreases
      ↓
Auto Scaling
      ↓
Less capacity
```

This helps maintain performance while controlling capacity.

---

# Global Secondary Index (GSI)

A **Global Secondary Index** allows you to query a table using a different key structure.

Suppose the main table uses:

```text
CustomerID
```

But you also need to search efficiently by:

```text
Email
```

A GSI can provide another access pattern.

```text
Main Table
CustomerID

GSI
Email
```

This avoids scanning the entire table for every email lookup.

---

# Local Secondary Index (LSI)

A **Local Secondary Index** uses the same partition key as the base table but provides a different sort key.

Conceptually:

```text
Base Table
Partition Key = CustomerID
Sort Key      = OrderID

LSI
Partition Key = CustomerID
Sort Key      = OrderDate
```

LSIs must be created when the table is created.

---

# GSI vs LSI

| Feature       | GSI                        | LSI                        |
| ------------- | -------------------------- | -------------------------- |
| Partition key | Can be different           | Same as base table         |
| Sort key      | Can be different           | Different                  |
| Creation      | Can be added later         | Must be created with table |
| Main purpose  | Alternative access pattern | Alternative sort order     |

For practical cloud work, **GSI is especially important to understand**.

---

# DynamoDB Streams

DynamoDB Streams captures changes made to items in a table.

Example:

```text
Item Changed
     ↓
DynamoDB Stream
     ↓
Lambda
     ↓
Process Change
```

Changes can include:

* Item created
* Item updated
* Item deleted

This is useful for event-driven architectures.

---

# DynamoDB + Lambda

DynamoDB and Lambda are commonly used together.

Example:

```text
Application
    |
    ↓
DynamoDB
    |
    ↓
DynamoDB Streams
    |
    ↓
Lambda
    |
    ↓
Additional Processing
```

Example use cases:

* Send notifications
* Update another system
* Process new records
* Maintain derived data
* Trigger workflows

---

# DynamoDB Transactions

DynamoDB supports transactional operations when multiple changes need to succeed together.

Conceptually:

```text
Operation A
     +
Operation B
     ↓
Transaction
```

If the transaction cannot complete successfully, the required operations do not partially succeed.

This is useful when maintaining consistency across related changes.

---

# DynamoDB Global Tables

DynamoDB Global Tables allow data to be replicated across multiple AWS Regions.

Example:

```text
                 DynamoDB
                    |
        ┌───────────┴───────────┐
        ↓                       ↓
   ap-south-1              us-east-1
```

This can help applications provide:

* Multi-Region availability
* Low-latency access for global users
* Disaster recovery capabilities

---

# DynamoDB Backup

DynamoDB supports backup and recovery capabilities.

Important features include:

* Point-in-time recovery
* On-demand backups

Point-in-time recovery can help restore a table to an earlier point within the supported recovery window.

---

# DynamoDB Encryption

DynamoDB provides encryption at rest.

AWS manages the encryption infrastructure, and AWS KMS can be used for key management options.

Conceptually:

```text
Application
    ↓
DynamoDB
    ↓
Encrypted Storage
```

---

# DynamoDB Security

Access to DynamoDB can be controlled using IAM.

Example permissions include:

```text
dynamodb:GetItem
dynamodb:PutItem
dynamodb:UpdateItem
dynamodb:DeleteItem
dynamodb:Query
dynamodb:Scan
```

Use least-privilege IAM policies so applications only have the permissions they require.

---

# DynamoDB Availability

DynamoDB is designed as a highly available managed AWS service.

AWS manages the underlying infrastructure and automatically handles much of the availability and scaling complexity.

This makes DynamoDB suitable for applications requiring highly available database access without managing database servers.

---

# DynamoDB vs RDS

This is an important SAA comparison.

| Feature           | DynamoDB                                   | RDS                      |
| ----------------- | ------------------------------------------ | ------------------------ |
| Database type     | NoSQL                                      | Relational               |
| Data model        | Key-value/document                         | Tables/rows/columns      |
| SQL               | No traditional SQL model                   | SQL                      |
| Schema            | Flexible                                   | Structured               |
| Scaling           | Designed for large-scale automatic scaling | Depends on configuration |
| Joins             | Not traditional relational joins           | Supported                |
| Transactions      | Supported                                  | Supported                |
| Server management | Fully managed                              | Fully managed            |
| Best for          | High-scale key/value access                | Relational workloads     |

Simple rule:

```text
Need relational SQL database?
        ↓
       RDS

Need scalable NoSQL key-value/document database?
        ↓
     DynamoDB
```

---

# DynamoDB vs Aurora

### Aurora

Best when the application needs:

* Relational database
* SQL
* Complex queries
* Joins
* Relational data model

### DynamoDB

Best when the application needs:

* NoSQL
* Very low-latency access
* Massive scale
* Key-value/document data
* Flexible schema

---

# DynamoDB and Caching

DynamoDB can work with **DynamoDB Accelerator (DAX)**.

DAX is a managed, in-memory cache designed specifically for DynamoDB.

Conceptually:

```text
Application
    |
    ↓
   DAX
    |
    ↓
DynamoDB
```

DAX can reduce read latency for workloads that benefit from caching.

---

# DynamoDB and S3

DynamoDB is not intended to replace S3 for large objects.

For example, an application could store:

```text
DynamoDB
---------
Booking ID
Customer ID
Image URL
Status
```

while storing the actual image in:

```text
S3
```

Architecture:

```text
Application
   |
   ├── DynamoDB → Metadata
   |
   └── S3 → Files/Objects
```

---

# DynamoDB in a Web Application

A typical architecture could look like:

```text
User
 |
 ↓
Application
 |
 ↓
DynamoDB
```

For a serverless architecture:

```text
User
 |
 ↓
CloudFront
 |
 ↓
API Gateway
 |
 ↓
Lambda
 |
 ↓
DynamoDB
```

This provides a serverless application stack.

---

# DynamoDB and Sheepeye

Your current Sheepeye project uses **Neon PostgreSQL**, which is a relational database.

You do not need to replace it with DynamoDB.

Your current architecture:

```text
Sheepeye
   |
   ↓
Application
   |
   ↓
Neon PostgreSQL
```

DynamoDB would make sense if the application had a specific NoSQL use case, such as highly scalable key-value data.

For your portfolio, understanding DynamoDB is more important than adding it unnecessarily.

---

# Common SAA Scenarios

## Scenario 1: Massive-scale NoSQL application

> An application requires a highly scalable NoSQL database with very low latency.

Use:

```text
DynamoDB
```

---

## Scenario 2: Unpredictable traffic

> An application has unpredictable traffic and the team does not want to manage database capacity.

Consider:

```text
DynamoDB On-Demand
```

---

## Scenario 3: Alternative query pattern

> A DynamoDB table is primarily keyed by CustomerID, but the application frequently needs to query customers by Email.

Use:

```text
Global Secondary Index
```

---

## Scenario 4: React to database changes

> Whenever an item changes in DynamoDB, a Lambda function should process the change.

Use:

```text
DynamoDB
    ↓
DynamoDB Streams
    ↓
Lambda
```

---

## Scenario 5: Global application

> Users around the world need access to the same DynamoDB data with high availability.

Consider:

```text
DynamoDB Global Tables
```

---

## Scenario 6: Relational workload

> An application requires SQL queries, joins, and complex relational relationships.

DynamoDB is not the best choice.

Consider:

```text
RDS / Aurora
```

---

# Key Points

* DynamoDB is a **fully managed serverless NoSQL database**.
* It uses **tables, items and attributes**.
* Every table has a primary key.
* Primary keys can be a **partition key** or **partition key + sort key**.
* Query is generally preferred over Scan when retrieving specific data.
* GSIs provide alternative access patterns.
* LSIs use the same partition key with a different sort key.
* DynamoDB supports **provisioned** and **on-demand** capacity modes.
* DynamoDB Auto Scaling can adjust provisioned capacity.
* DynamoDB Streams captures item-level changes.
* Streams can trigger Lambda-based processing.
* Global Tables provide multi-Region replication.
* Point-in-time recovery and on-demand backups support data protection.
* DynamoDB supports encryption at rest.
* IAM controls access to DynamoDB resources and operations.
* DAX provides an in-memory caching layer for DynamoDB.
* **DynamoDB = scalable NoSQL.**
* **RDS/Aurora = relational SQL.**
* For SAA, think of DynamoDB when the requirement is **serverless, highly scalable, low-latency NoSQL data access**.
