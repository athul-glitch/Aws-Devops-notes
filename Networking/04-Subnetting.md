# Subnetting

## Introduction

Subnetting is the process of dividing a larger network into smaller networks called **subnets**.

Subnetting is important for cloud engineers because AWS VPCs are divided into subnets.

Subnetting helps with:

* Organizing networks
* Separating resources
* Controlling traffic
* Improving security
* Efficiently using IP addresses

---

# 1. What is a Subnet?

A subnet is a smaller network created from a larger network.

For example:

```text
VPC
10.0.0.0/16
      |
      +-- Public Subnet
      |   10.0.1.0/24
      |
      +-- Private Subnet
          10.0.2.0/24
```

The VPC is the larger network.

The subnets are smaller networks inside the VPC.

---

# 2. Why Do We Use Subnets?

Subnets help us separate resources.

For example:

```text
Public Subnet
    |
    +-- Web Server

Private Subnet
    |
    +-- Database
```

A common cloud architecture is:

```text
Internet
    |
    ↓
Public Subnet
    |
    ↓
Application
    |
    ↓
Private Subnet
    |
    ↓
Database
```

This improves network organization and security.

---

# 3. CIDR and Subnetting

Subnetting uses CIDR notation.

Example:

```text
10.0.0.0/16
```

This could be divided into smaller networks such as:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

Each `/24` is a separate subnet.

---

# 4. Understanding the Prefix

The number after `/` tells us how many bits belong to the network portion.

Example:

```text
10.0.0.0/24
```

`/24` means:

```text
24 bits → Network
8 bits  → Host
```

IPv4 has 32 bits in total.

Therefore:

```text
32 - 24 = 8 host bits
```

---

# 5. Calculating Total IP Addresses

The formula is:

```text
Total addresses = 2^(host bits)
```

For `/24`:

```text
32 - 24 = 8

2^8 = 256
```

Therefore:

```text
/24 = 256 total IP addresses
```

---

# 6. Common Subnet Sizes

| CIDR | Host Bits | Total IPs |
| ---- | --------: | --------: |
| /16  |        16 |    65,536 |
| /20  |        12 |     4,096 |
| /21  |        11 |     2,048 |
| /22  |        10 |     1,024 |
| /23  |         9 |       512 |
| /24  |         8 |       256 |
| /25  |         7 |       128 |
| /26  |         6 |        64 |
| /27  |         5 |        32 |
| /28  |         4 |        16 |
| /29  |         3 |         8 |
| /30  |         2 |         4 |

For AWS, you will commonly see subnet sizes such as:

```text
/24
/25
/26
/27
/28
```

---

# 7. /24 Example

Consider:

```text
192.168.1.0/24
```

Total addresses:

```text
256
```

Range:

```text
192.168.1.0
-
192.168.1.255
```

In a traditional IPv4 network:

```text
Network address:
192.168.1.0

Usable host addresses:
192.168.1.1 - 192.168.1.254

Broadcast address:
192.168.1.255
```

---

# 8. /25 Subnet

A `/25` has:

```text
32 - 25 = 7 host bits
```

Therefore:

```text
2^7 = 128 total addresses
```

A `/24` can be divided into two `/25` subnets.

Example:

```text
192.168.1.0/24
```

becomes:

```text
Subnet 1:
192.168.1.0/25

Subnet 2:
192.168.1.128/25
```

Ranges:

```text
Subnet 1:
192.168.1.0 - 192.168.1.127

Subnet 2:
192.168.1.128 - 192.168.1.255
```

---

# 9. /26 Subnet

A `/26` has:

```text
32 - 26 = 6 host bits
```

Therefore:

```text
2^6 = 64 total addresses
```

A `/24` can be divided into four `/26` subnets.

Example:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

The ranges are:

```text
192.168.1.0   - 192.168.1.63
192.168.1.64  - 192.168.1.127
192.168.1.128 - 192.168.1.191
192.168.1.192 - 192.168.1.255
```

---

# 10. /27 Subnet

A `/27` has:

```text
32 - 27 = 5 host bits
```

Therefore:

```text
2^5 = 32 total addresses
```

A `/24` can be divided into eight `/27` subnets.

The ranges increase by 32:

```text
192.168.1.0/27
192.168.1.32/27
192.168.1.64/27
192.168.1.96/27
192.168.1.128/27
192.168.1.160/27
192.168.1.192/27
192.168.1.224/27
```

---

# 11. /28 Subnet

A `/28` has:

```text
32 - 28 = 4 host bits
```

Therefore:

```text
2^4 = 16 total addresses
```

A `/24` can be divided into sixteen `/28` subnets.

Example:

```text
192.168.1.0/28
192.168.1.16/28
192.168.1.32/28
192.168.1.48/28
...
192.168.1.240/28
```

Each subnet contains 16 total IPv4 addresses.

---

# 12. AWS Subnetting

AWS VPCs use subnetting to divide a VPC into smaller networks.

Example:

```text
VPC
10.0.0.0/16
```

We can create:

```text
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24

Database Subnet:
10.0.3.0/24
```

Each subnet has its own IP range.

---

# 13. Public and Private Subnets

A subnet itself is not automatically public or private.

The routing configuration determines whether it is public or private.

### Public Subnet

A subnet is commonly considered public when its route table has a route to an Internet Gateway.

Example:

```text
0.0.0.0/0 → Internet Gateway
```

Resources in the subnet can potentially communicate with the Internet if their other configuration allows it.

### Private Subnet

A private subnet does not have a direct route to an Internet Gateway.

It may use a NAT Gateway for outbound Internet access.

Example:

```text
Private Subnet
      |
      ↓
 NAT Gateway
      |
      ↓
Internet Gateway
      |
      ↓
Internet
```

---

# 14. AWS Reserved IP Addresses

AWS reserves **5 IPv4 addresses** in every subnet.

For example:

```text
10.0.1.0/24
```

contains:

```text
256 total addresses
```

AWS reserves 5.

Therefore:

```text
256 - 5 = 251 usable IPv4 addresses
```

The five reserved addresses are the first four addresses and the last address in the subnet.

For:

```text
10.0.1.0/24
```

AWS reserves:

```text
10.0.1.0
10.0.1.1
10.0.1.2
10.0.1.3
10.0.1.255
```

So usable addresses are:

```text
10.0.1.4 - 10.0.1.254
```

---

# 15. AWS Subnet Size Restrictions

When creating an IPv4 subnet in an AWS VPC, the subnet must be between:

```text
/16 and /28
```

For example:

```text
10.0.1.0/24  → Valid
10.0.1.0/28  → Valid
```

A subnet smaller than `/28`, such as `/29`, is not valid for an AWS IPv4 subnet.

---

# 16. Subnetting Example

Suppose we have:

```text
VPC:
10.0.0.0/16
```

We need four subnets.

We can create:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

Architecture:

```text
             VPC
          10.0.0.0/16
               |
     +---------+---------+
     |         |         |
     ↓         ↓         ↓
 Public     Private    Database
 Subnet     Subnet      Subnet
10.0.1.0/24 10.0.2.0/24 10.0.3.0/24
```

---

# 17. Subnetting and Availability Zones

AWS subnets are created inside a single Availability Zone.

For high availability, applications can use subnets in multiple Availability Zones.

Example:

```text
VPC
10.0.0.0/16

        +-------------------+
        |                   |
        ↓                   ↓
      AZ-1                 AZ-2
        |                   |
        ↓                   ↓
10.0.1.0/24           10.0.2.0/24
Public Subnet         Public Subnet
```

A common AWS architecture uses multiple Availability Zones for better availability.

---

# 18. Avoiding Overlapping Subnets

Subnets inside the same VPC should not overlap.

Bad example:

```text
10.0.1.0/24
10.0.1.0/25
```

These ranges overlap.

Good example:

```text
10.0.1.0/24
10.0.2.0/24
```

The ranges are separate.

---

# 19. Simple Subnetting Calculation

Suppose you have:

```text
192.168.1.0/24
```

and want **4 equal subnets**.

A `/24` has 256 addresses.

To create 4 subnets:

```text
256 ÷ 4 = 64
```

Each subnet gets 64 addresses.

Therefore, the new prefix is:

```text
/26
```

The four subnets are:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 20. Quick Subnetting Method

For simple subnet calculations:

### Step 1

Identify the original CIDR.

Example:

```text
192.168.1.0/24
```

### Step 2

Determine how many subnets you need.

Example:

```text
4 subnets
```

### Step 3

Find the new prefix.

```text
/24 → /26
```

### Step 4

Calculate the block size.

For `/26`:

```text
64 addresses
```

### Step 5

List the subnet ranges.

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 21. Subnetting in Real Cloud Architecture

A simple AWS application might use:

```text
VPC
10.0.0.0/16
       |
       +-- Public Subnet
       |   10.0.1.0/24
       |   EC2 / Load Balancer
       |
       +-- Private Subnet
       |   10.0.2.0/24
       |   Application Server
       |
       +-- Database Subnet
           10.0.3.0/24
           RDS
```

The exact architecture depends on the application.

The important idea is that different resources can be placed into different network segments.

---

# 22. Common Subnetting Terms

### Network Address

Identifies the subnet.

Example:

```text
10.0.1.0/24
```

Network address:

```text
10.0.1.0
```

### Host Address

Identifies a device/resource inside the subnet.

Example:

```text
10.0.1.10
```

### Subnet Mask

Defines which part of an IP address belongs to the network.

Example:

```text
/24
```

is equivalent to:

```text
255.255.255.0
```

### CIDR

A shorter way to represent the network and prefix length.

Example:

```text
10.0.1.0/24
```

---

# Key Points

* Subnetting divides a larger network into smaller networks.
* AWS VPCs are divided into subnets.
* CIDR determines the size of a subnet.
* `/24` contains 256 total IPv4 addresses.
* `/25` contains 128 total IPv4 addresses.
* `/26` contains 64 total IPv4 addresses.
* `/27` contains 32 total IPv4 addresses.
* `/28` contains 16 total IPv4 addresses.
* AWS reserves 5 IPv4 addresses in every subnet.
* AWS IPv4 subnets can range from `/16` to `/28`.
* Subnets must not overlap.
* Each AWS subnet belongs to one Availability Zone.
* Multiple Availability Zones can be used for high availability.
* A public subnet normally has a route to an Internet Gateway.
* A private subnet does not have a direct route to an Internet Gateway.
* NAT Gateway can provide outbound Internet access for private resources.

## Cloud Engineer Focus

For AWS and cloud support roles, understand these especially well:

```text
VPC
Subnet
CIDR
/16
/24
/25
/26
/27
/28
Usable IP addresses
AWS reserved IPs
Public subnet
Private subnet
Availability Zone
Subnet overlap
```

The main goal is to be able to look at a VPC such as:

```text
10.0.0.0/16
```

and understand how it can be divided into smaller networks such as:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

and how those subnets can be used to organize AWS resources.
