# VPC Peering

## What is VPC Peering?

**VPC Peering** allows two Amazon VPCs to communicate with each other privately using AWS networking infrastructure.

The traffic does not need to travel through the public internet.

Example:

```text
VPC A
10.0.0.0/16
     |
     | VPC Peering
     |
VPC B
10.1.0.0/16
```

Resources in VPC A can communicate with resources in VPC B when the appropriate routes and security rules are configured.

---

# Why VPC Peering is Used

VPC Peering is useful when two VPCs need private network communication.

Common examples:

* Application VPC → Database VPC
* Production VPC → Shared services VPC
* Development VPC → Testing VPC
* Two VPCs belonging to different AWS accounts
* Communication between VPCs in different AWS Regions

Example:

```text
Application VPC
       |
       | Private communication
       v
Database VPC
```

---

# Basic Architecture

Suppose we have:

```text
VPC A
10.0.0.0/16

VPC B
10.1.0.0/16
```

Create a VPC Peering connection:

```text
10.0.0.0/16
      |
      | Peering
      |
10.1.0.0/16
```

Then configure routes in both VPCs.

---

# VPC Peering is Not Automatically Routing

Creating a VPC Peering connection alone is not enough.

You also need appropriate routes.

Example:

### VPC A route table

```text
Destination       Target

10.0.0.0/16       local
10.1.0.0/16       VPC Peering
```

### VPC B route table

```text
Destination       Target

10.1.0.0/16       local
10.0.0.0/16       VPC Peering
```

Both sides need routes for communication.

---

# Security Groups

Security Groups can also affect communication across VPC Peering.

Example:

```text
VPC A
EC2
 |
 | TCP 443
 v
VPC B
EC2
```

The destination EC2 security group must allow the required traffic from the source.

For example:

```text
Inbound rule:

Protocol: TCP
Port: 443
Source: 10.0.0.0/16
```

Security Groups are **stateful**, so return traffic is automatically allowed for an established connection.

---

# Network ACLs

Network ACLs can also affect VPC Peering traffic.

If communication does not work, check:

```text
Route Table
     ↓
Security Group
     ↓
Network ACL
```

Make sure the required traffic is allowed.

---

# CIDR Ranges

VPC Peering requires careful IP address planning.

The VPC CIDR ranges should **not overlap** for normal VPC Peering communication.

Good example:

```text
VPC A
10.0.0.0/16

VPC B
10.1.0.0/16
```

Bad example:

```text
VPC A
10.0.0.0/16

VPC B
10.0.0.0/16
```

The overlapping address ranges create routing ambiguity.

Therefore, plan VPC CIDRs carefully before creating multiple VPCs.

---

# Same-Region VPC Peering

VPCs can be peered within the same AWS Region.

Example:

```text
ap-south-1

VPC A
   |
Peering
   |
VPC B
```

The VPCs can belong to:

* The same AWS account
* Different AWS accounts

---

# Cross-Region VPC Peering

VPC Peering can also connect VPCs in different AWS Regions.

Example:

```text
Mumbai Region
VPC A
   |
   | VPC Peering
   |
Singapore Region
VPC B
```

Traffic remains on the AWS network rather than traveling through the public internet.

Cross-region peering can be useful for:

* Global applications
* Disaster recovery
* Regional services
* Cross-region private communication

---

# Cross-Account VPC Peering

VPCs can belong to different AWS accounts.

Example:

```text
Account A
VPC A
   |
   | Peering
   |
Account B
VPC B
```

The VPC owner can request a peering connection with a VPC owned by another account.

The other account must accept the request.

---

# VPC Peering Connection States

A VPC Peering connection generally goes through states such as:

```text
Pending Acceptance
        |
        v
     Active
```

The peering connection becomes usable after the required acceptance and configuration steps are completed.

---

# Non-Transitive Routing

One of the most important VPC Peering concepts is:

**VPC Peering is not transitive.**

Consider:

```text
VPC A
  |
Peering
  |
VPC B
  |
Peering
  |
VPC C
```

VPC A cannot automatically use VPC B as a router to reach VPC C.

This is **not supported** through normal VPC Peering.

You would need another architecture, such as:

```text
VPC A ←→ VPC C
```

or a centralized networking service such as Transit Gateway.

---

# VPC Peering vs Transit Gateway

These services solve related but different networking problems.

### VPC Peering

Best suited for:

```text
VPC A ←→ VPC B
```

Direct VPC-to-VPC communication.

### Transit Gateway

Best suited for:

```text
          VPC A
             |
             |
VPC B ---- Transit Gateway ---- VPC C
             |
             |
          VPC D
```

It provides a centralized network hub.

---

# Example: Few VPCs

Suppose a company has three VPCs:

```text
VPC A
VPC B
VPC C
```

With VPC Peering, you may need several connections depending on which VPCs need to communicate.

```text
VPC A ←→ VPC B
  ↕       ↕
VPC C
```

As the number of VPCs increases, managing many individual peering connections becomes more complicated.

Transit Gateway can provide a simpler hub-and-spoke design.

---

# VPC Peering vs Transit Gateway

| Feature             | VPC Peering       | Transit Gateway          |
| ------------------- | ----------------- | ------------------------ |
| Connection model    | Direct            | Central hub              |
| Two VPCs            | Excellent         | Works                    |
| Many VPCs           | Less convenient   | Excellent                |
| Transitive routing  | No                | Yes, through TGW routing |
| Centralized routing | No                | Yes                      |
| Management at scale | More complex      | Easier                   |
| Typical use         | Simple VPC-to-VPC | Large multi-VPC networks |

---

# VPC Peering and Internet Gateway

VPC Peering does not require traffic to go through an Internet Gateway.

Example:

```text
VPC A
  |
  | VPC Peering
  |
VPC B
```

The communication is private.

You do not need:

```text
VPC A
 |
Internet Gateway
 |
Internet
 |
Internet Gateway
 |
VPC B
```

---

# VPC Peering and NAT Gateway

NAT Gateway is not required for VPC Peering communication.

Example:

```text
VPC A
EC2
 |
VPC Peering
 |
VPC B
EC2
```

NAT Gateway is used for outbound internet access from private resources.

VPC Peering is used for private communication between VPCs.

---

# VPC Peering and DNS

VPC Peering can support DNS-related communication when the appropriate DNS settings are configured.

For example:

```text
VPC A
Application
   |
   | DNS resolution
   |
VPC B
Service
```

DNS support and hostname resolution options should be configured correctly when private DNS names need to work across peered VPCs.

---

# Example Architecture

A company may separate workloads into different VPCs.

```text
                 AWS Region
                     |
        +------------+------------+
        |                         |
     VPC A                     VPC B
  Application                Database
        |                         |
        +-------- Peering --------+
```

The application can communicate privately with the database.

---

# SAA Scenario

### Requirement

A company has an application running in VPC A and a database running in VPC B.

Both VPCs have non-overlapping CIDR ranges.

The company wants private communication without using the public internet.

### Solution

Use:

```text
VPC A
  |
VPC Peering
  |
VPC B
```

Then configure:

* VPC Peering
* Route tables
* Security Groups
* Network ACLs if required

---

# SAA Scenario: Overlapping CIDRs

### Requirement

Two VPCs have:

```text
VPC A → 10.0.0.0/16
VPC B → 10.0.0.0/16
```

They need private communication.

### Problem

The CIDR ranges overlap.

Standard VPC Peering cannot provide normal routing between these overlapping networks.

The company should redesign the CIDR ranges or use another suitable architecture depending on the requirement.

---

# SAA Scenario: Many VPCs

### Requirement

A company has dozens of VPCs and wants centralized connectivity.

Using individual peering connections would become difficult to manage.

### Suitable solution

Use:

```text
             VPC A
                |
             VPC B
                |
                v
        Transit Gateway
                ^
                |
             VPC C
                |
             VPC D
```

Transit Gateway is generally more appropriate for large-scale VPC connectivity.

---

# VPC Peering Troubleshooting

If resources in two peered VPCs cannot communicate, check the following.

### 1. Peering status

Confirm the connection is:

```text
Active
```

### 2. CIDR ranges

Confirm the VPC CIDRs do not overlap.

### 3. Route tables

Check that each VPC has a route to the other VPC through the peering connection.

### 4. Security Groups

Confirm the destination security group allows the required source traffic.

### 5. Network ACLs

Check whether NACL rules are blocking the traffic.

### 6. DNS

If using private DNS names, check DNS settings.

### 7. Application port

Make sure the destination service is actually listening on the expected port.

Example:

```text
TCP 443
TCP 80
TCP 3306
TCP 5432
```

---

# VPC Peering with Terraform

VPC Peering can be managed using Terraform.

Typical infrastructure:

```text
VPC A
 |
+-- Route Table
|
VPC Peering
|
+-- Route Table
 |
VPC B
```

Terraform can manage:

* VPCs
* VPC Peering connections
* Routes
* Security Groups
* Network ACLs

A typical Terraform workflow is:

```text
Terraform configuration
        |
        v
terraform plan
        |
        v
terraform apply
        |
        v
VPC Peering + Routes
```

This makes the networking configuration reproducible.

---

# Sheepeye Relevance

VPC Peering is **not currently required** for the Sheepeye project.

The current architecture uses a single main VPC/network environment.

A future architecture could use multiple VPCs, for example:

```text
Production VPC
      |
VPC Peering
      |
Shared Services VPC
```

However, adding VPC Peering only for portfolio complexity is unnecessary.

For Sheepeye, it is more important to understand:

* VPC
* Subnets
* Route Tables
* Internet Gateway
* NAT Gateway
* Security Groups
* Terraform

before introducing multi-VPC architecture.

---

# Key Takeaways

* VPC Peering provides private communication between two VPCs.
* It can work within the same Region or across Regions.
* VPCs can belong to the same or different AWS accounts.
* Peering traffic does not need to travel over the public internet.
* Route tables must be configured on both sides.
* Security Groups and NACLs can affect communication.
* VPC CIDR ranges should not overlap.
* VPC Peering is **not transitive**.
* NAT Gateway is not required for VPC Peering.
* Internet Gateways are not required for the peering connection itself.
* VPC Peering is useful for simple VPC-to-VPC connectivity.
* Transit Gateway is generally better for large numbers of VPCs.
* Terraform can manage VPC Peering and its routes.
* VPC Peering is useful to know for AWS Solutions Architect scenarios.
