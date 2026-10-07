# Amazon VPC

## What is Amazon VPC?

Amazon Virtual Private Cloud (VPC) is a logically isolated virtual network in AWS where you can launch and control AWS resources.

A VPC allows you to control:

* IP address ranges
* Subnets
* Route tables
* Internet connectivity
* Network security
* Traffic routing

---

## VPC Components

A typical VPC can contain:

* CIDR block
* Public subnets
* Private subnets
* Route tables
* Internet Gateway
* NAT Gateway
* Security Groups
* Network ACLs
* VPC Endpoints
* Availability Zones

---

## CIDR Block

A VPC requires an IPv4 CIDR block that defines its private IP address range.

Example:

```text
10.0.0.0/16
```

This provides a large private IP range that can be divided into smaller subnet ranges.

Example subnet ranges:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

### Common Private IPv4 Ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

---

## Subnets

A subnet is a smaller network inside a VPC.

Subnets are associated with an Availability Zone.

There are two common types:

* Public subnet
* Private subnet

### Public Subnet

A public subnet has a route to an Internet Gateway.

Example:

```text
Internet
   |
Internet Gateway
   |
Public Subnet
   |
EC2
```

A common use case is a web server or load balancer.

### Private Subnet

A private subnet does not have a direct route to the Internet Gateway.

Example:

```text
Internet
   |
Internet Gateway
   |
Public Subnet
   |
NAT Gateway
   |
Private Subnet
   |
EC2
```

Private subnets are commonly used for application servers and databases.

---

## Availability Zones

An Availability Zone (AZ) is an isolated location within an AWS Region.

A VPC can span multiple Availability Zones.

Example:

```text
Region: ap-south-1

VPC
 |
 +-- AZ-a
 |    +-- Public Subnet
 |    +-- Private Subnet
 |
 +-- AZ-b
      +-- Public Subnet
      +-- Private Subnet
```

Using multiple Availability Zones improves availability and fault tolerance.

---

## Internet Gateway

An Internet Gateway (IGW) allows communication between resources in a VPC and the public Internet.

For a subnet to be considered public, its route table must contain a route to an Internet Gateway.

Example:

```text
0.0.0.0/0 → Internet Gateway
```

An Internet Gateway is attached to the VPC.

---

## Route Tables

A route table controls where network traffic is directed.

Example public subnet route table:

```text
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

The `local` route allows communication within the VPC.

The `0.0.0.0/0` route represents all IPv4 destinations not covered by a more specific route.

---

## NAT Gateway

A NAT Gateway allows resources in a private subnet to access the Internet for outbound connections without allowing unsolicited inbound Internet connections.

Example:

```text
Private EC2
    |
Private Route Table
    |
NAT Gateway
    |
Internet Gateway
    |
Internet
```

A NAT Gateway is normally placed in a public subnet.

### Important

NAT Gateway is mainly used for outbound Internet access from private resources.

For example, a private EC2 instance may need Internet access to download software updates.

---

## Security Groups

A Security Group acts as a virtual firewall for AWS resources such as EC2 instances.

Security Groups:

* Control inbound traffic
* Control outbound traffic
* Are stateful
* Allow rules only
* Can reference other Security Groups

Example:

```text
EC2 Security Group

Inbound:
SSH   TCP 22    → My IP
HTTP  TCP 80    → 0.0.0.0/0
HTTPS TCP 443   → 0.0.0.0/0
```

### Stateful

If inbound traffic is allowed, the response traffic is automatically allowed.

---

## Network ACL

A Network Access Control List (NACL) is a firewall associated with a subnet.

NACLs:

* Control inbound traffic
* Control outbound traffic
* Are stateless
* Support allow and deny rules
* Work at subnet level

### Security Group vs NACL

| Feature         | Security Group       | NACL                      |
| --------------- | -------------------- | ------------------------- |
| Level           | Instance/ENI         | Subnet                    |
| Stateful        | Yes                  | No                        |
| Allow rules     | Yes                  | Yes                       |
| Deny rules      | No explicit deny     | Yes                       |
| Rule evaluation | All applicable rules | Rules evaluated by number |

---

## VPC Peering

VPC Peering allows two VPCs to communicate privately using private IP addresses.

Example:

```text
VPC A
10.0.0.0/16
     |
 VPC Peering
     |
VPC B
10.1.0.0/16
```

The VPC CIDR ranges should not overlap.

VPC Peering is useful when two VPCs need direct private communication.

---

## Transit Gateway

AWS Transit Gateway provides a central network hub for connecting multiple VPCs and other networks.

Example:

```text
VPC A
   |
   |
VPC B --- Transit Gateway --- VPC C
   |
   |
On-premises Network
```

Transit Gateway is useful for larger environments with many VPCs.

---

## VPC Endpoints

VPC Endpoints allow private connectivity between a VPC and supported AWS services without requiring Internet access.

For example, an EC2 instance in a private subnet can access Amazon S3 through a VPC endpoint.

This can reduce the need for Internet or NAT Gateway connectivity for AWS service access.

### Common Types

#### Gateway Endpoint

Commonly used for:

* Amazon S3
* Amazon DynamoDB

#### Interface Endpoint

Uses private network interfaces and is commonly used for many AWS services.

---

## Public vs Private EC2

### Public EC2

A typical public EC2 instance requires:

* Public subnet
* Route to Internet Gateway
* Public IPv4 address or Elastic IP
* Security Group allowing required inbound traffic

Example:

```text
Internet
   |
Internet Gateway
   |
Public Subnet
   |
EC2
```

### Private EC2

A private EC2 instance does not need a public IP.

For outbound Internet access, it can use a NAT Gateway.

```text
Internet
   |
Internet Gateway
   |
NAT Gateway
   |
Private Subnet
   |
EC2
```

---

## Three-Tier Architecture

A common AWS architecture separates workloads into three layers.

```text
Internet
   |
Application Load Balancer
   |
Public Subnet
   |
Private Application Subnet
   |
Private Database Subnet
```

### Web Tier

Usually contains:

* Application Load Balancer
* Web-facing components

### Application Tier

Usually contains:

* EC2 instances
* Application servers
* Containers

### Database Tier

Usually contains:

* Amazon RDS
* Aurora
* Other database services

The application and database tiers are commonly placed in private subnets.

---

## Example VPC Architecture

A simple production-style architecture can look like:

```text
                         Internet
                            |
                     Internet Gateway
                            |
                +-----------+-----------+
                |                       |
          Public Subnet A         Public Subnet B
                |                       |
          Load Balancer           Load Balancer
                |                       |
                +-----------+-----------+
                            |
                    Private Subnets
                       /         \
                      /           \
               EC2 App A        EC2 App B
                      \           /
                       \         /
                       Database
```

For outbound Internet access from private resources:

```text
Private EC2
    |
Private Route Table
    |
NAT Gateway
    |
Public Route Table
    |
Internet Gateway
    |
Internet
```

---

## Important VPC Concepts

### VPC

Logical network in AWS.

### CIDR

Defines the IP address range of the VPC or subnet.

### Subnet

A smaller IP range inside a VPC.

### Availability Zone

An isolated location within an AWS Region.

### Internet Gateway

Provides Internet connectivity for resources with appropriate public routing.

### NAT Gateway

Provides outbound Internet access from private subnets.

### Route Table

Controls network traffic routing.

### Security Group

Stateful firewall associated with network interfaces/resources.

### NACL

Stateless firewall operating at subnet level.

### VPC Peering

Private connection between two VPCs.

### Transit Gateway

Centralized network hub for connecting multiple VPCs and networks.

### VPC Endpoint

Private connection from a VPC to supported AWS services.

---

## Practical Example

A common AWS web application can use:

```text
VPC
10.0.0.0/16

Public Subnets
10.0.1.0/24
10.0.2.0/24

Private Application Subnets
10.0.11.0/24
10.0.12.0/24

Private Database Subnets
10.0.21.0/24
10.0.22.0/24
```

Architecture:

```text
                    Internet
                       |
                Internet Gateway
                       |
              Public Subnets
                       |
               Load Balancer
                       |
             Private App Subnets
                       |
                    EC2
                       |
             Private DB Subnets
                       |
                    RDS
```

This design keeps application and database resources away from direct public Internet access.

---

## VPC Traffic Flow

When troubleshooting connectivity, check these components:

```text
Client
  |
Security Group
  |
Subnet
  |
Route Table
  |
Internet Gateway / NAT Gateway / VPC Endpoint
  |
Destination
```

A connectivity problem can be caused by an incorrect route, Security Group rule, NACL rule, subnet configuration, or missing gateway/endpoint.

---

## Key Takeaways

* VPC provides an isolated network environment in AWS.
* CIDR defines the IP address range.
* Subnets divide the VPC into smaller networks.
* Public subnets have a route to an Internet Gateway.
* Private subnets do not have direct Internet Gateway routes.
* NAT Gateway provides outbound Internet access for private resources.
* Route tables control traffic paths.
* Security Groups are stateful.
* NACLs are stateless.
* VPC Peering connects two VPCs privately.
* Transit Gateway connects multiple VPCs and networks through a central hub.
* VPC Endpoints provide private access to supported AWS services.
* Multiple Availability Zones improve availability and fault tolerance.

