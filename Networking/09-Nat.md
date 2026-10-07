# NAT

## Introduction

**NAT (Network Address Translation)** allows private IP addresses to communicate with other networks by translating network addresses.

NAT is commonly used when private resources need to access the internet without having public IP addresses.

NAT is an important concept for AWS VPC networking.

---

# 1. Why NAT is Needed

Private IP addresses are not directly routable over the public internet.

For example:

```text
Private EC2
10.0.2.10
     |
     v
   NAT
     |
     v
Internet
```

NAT translates the private source address so the resource can communicate with an external destination.

---

# 2. Simple NAT Example

Suppose an EC2 instance has:

```text
Private IP: 10.0.2.10
```

The instance wants to access:

```text
https://example.com
```

The traffic can follow:

```text
Private EC2
     |
     | Private IP
     v
NAT Gateway
     |
     | Public connectivity
     v
Internet
```

The external destination does not need direct access to the private EC2 instance.

---

# 3. NAT in AWS

AWS provides **NAT Gateway** as a managed NAT service.

A common AWS architecture is:

```text
                Internet
                    |
                    v
             Internet Gateway
                    |
                    v
              NAT Gateway
                    |
                    v
             Private Subnet
                    |
                    v
                  EC2
```

The private EC2 instance can initiate outbound connections through the NAT Gateway.

---

# 4. NAT Gateway

A **NAT Gateway** is an AWS-managed service that allows resources in a private subnet to access external networks.

A typical setup requires:

* NAT Gateway
* Public subnet for the NAT Gateway
* Internet Gateway
* Elastic IP for a public NAT Gateway
* Route from the private subnet to the NAT Gateway

---

# 5. Private Subnet with NAT Gateway

Example:

```text
VPC
 |
 +---------------------+
 |                     |
 v                     v
Public Subnet      Private Subnet
 |                     |
 v                     v
NAT Gateway           EC2
 |                     |
 v                     |
Internet Gateway <-----+
 |
 v
Internet
```

More precisely, the private subnet's default route points to the NAT Gateway:

```text
0.0.0.0/0 → NAT Gateway
```

The NAT Gateway is placed in a public subnet that has a route to the Internet Gateway.

---

# 6. Why Put NAT Gateway in a Public Subnet?

A NAT Gateway needs a path to the internet.

A typical configuration is:

```text
Private Subnet
     |
     | 0.0.0.0/0
     v
NAT Gateway
     |
     v
Public Subnet
     |
     v
Internet Gateway
     |
     v
Internet
```

The NAT Gateway is associated with a public IP address through an Elastic IP.

---

# 7. Internet Gateway vs NAT Gateway

These two are different.

| Internet Gateway               | NAT Gateway                                             |
| ------------------------------ | ------------------------------------------------------- |
| Connects a VPC to the internet | Provides outbound internet access for private resources |
| Used by public subnet routing  | Commonly used by private subnet routing                 |
| Works with public addressing   | Allows private resources to reach external destinations |
| Attached to the VPC            | Deployed in a subnet                                    |

Simple idea:

```text
Internet Gateway
→ VPC internet connectivity

NAT Gateway
→ Private subnet outbound connectivity
```

---

# 8. NAT Gateway and Route Tables

The route table determines where traffic goes.

### Public subnet route table

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

### Private subnet route table

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

This creates the basic public/private subnet architecture.

---

# 9. Outbound Traffic

Suppose a private EC2 instance wants to download software updates.

```text
EC2
 |
 | Request
 v
NAT Gateway
 |
 v
Internet Gateway
 |
 v
Internet
```

The private instance can initiate the connection.

The external server does not receive the private EC2 address as a directly reachable internet address.

---

# 10. Inbound Traffic

A NAT Gateway is **not a general solution for allowing unsolicited inbound internet connections to a private EC2 instance**.

For example:

```text
Internet
   X
   |
NAT Gateway
   X
Private EC2
```

If you need users on the internet to access an application, a common design is to use a public-facing component such as:

* Application Load Balancer
* Network Load Balancer
* Public EC2
* CloudFront

depending on the architecture.

---

# 11. NAT Gateway and Security Groups

NAT does not replace security controls.

For example:

```text
Private EC2
    |
    v
Security Group
    |
    v
NAT Gateway
    |
    v
Internet
```

The EC2 Security Group must still allow the required outbound traffic.

Network ACLs and route tables also affect whether traffic works.

---

# 12. NAT Gateway and Availability Zones

For production architectures, NAT Gateway placement should be considered carefully.

A common high-availability design is to use a NAT Gateway in each Availability Zone.

Example:

```text
VPC
 |
 +-------------------------+
 |                         |
 v                         v
AZ-a                      AZ-b
 |                         |
NAT Gateway              NAT Gateway
 |                         |
Private Subnet            Private Subnet
```

Each private subnet can use the NAT Gateway in its own Availability Zone.

This can improve resilience and avoid unnecessary cross-AZ traffic.

---

# 13. NAT Gateway Cost

NAT Gateway is a **paid AWS service**.

Costs can include:

* Hourly NAT Gateway charges
* Data processing charges

Therefore, when practicing AWS with limited credits, avoid creating NAT Gateways unnecessarily.

For your Sheepeye project, this is especially important when you are experimenting with AWS infrastructure.

---

# 14. NAT vs Public IP

A private EC2 instance does not need its own public IP to access the internet through a NAT Gateway.

Example:

```text
Private EC2
10.0.2.10
     |
     v
NAT Gateway
     |
     v
Internet
```

This is one of the main reasons NAT is used in private subnets.

---

# 15. NAT Gateway vs NAT Instance

AWS historically supported NAT instances, where an EC2 instance was configured to perform NAT.

### NAT Gateway

* AWS managed
* Easier to operate
* Scales automatically
* Designed for production use
* No operating system administration required

### NAT Instance

* EC2-based
* Requires administration
* Requires patching
* Requires capacity planning
* More operational responsibility

For modern AWS architectures, **NAT Gateway is generally preferred** when NAT functionality is required.

---

# 16. Simple AWS Architecture

A typical architecture can look like:

```text
                         Internet
                            |
                            v
                    Internet Gateway
                            |
             +--------------+--------------+
             |                             |
             v                             v
       Public Subnet                 Public Subnet
             |                             |
        Load Balancer                NAT Gateway
             |                             |
             v                             v
       Private Subnet               Private Subnet
             |                             |
             v                             v
            EC2                           EC2
```

The exact architecture depends on the application.

---

# 17. NAT Troubleshooting

If a private EC2 instance cannot access the internet, check:

### 1. Route Table

Does the private subnet have:

```text
0.0.0.0/0 → NAT Gateway
```

### 2. NAT Gateway

Is the NAT Gateway available?

### 3. NAT Gateway Subnet

Is the NAT Gateway deployed in a public subnet?

### 4. Internet Gateway

Is the VPC attached to an Internet Gateway?

### 5. Public Subnet Route

Does the NAT Gateway's subnet have:

```text
0.0.0.0/0 → Internet Gateway
```

### 6. Elastic IP

Does the public NAT Gateway have the required public IP configuration?

### 7. Security Group

Does the EC2 Security Group allow the required outbound traffic?

### 8. Network ACL

Could the subnet-level Network ACL be blocking traffic?

---

# 18. NAT and Public vs Private Subnets

A simple way to remember:

```text
Public Subnet
     |
     v
Internet Gateway
     |
     v
Internet
```

Private subnet:

```text
Private Subnet
     |
     v
NAT Gateway
     |
     v
Internet Gateway
     |
     v
Internet
```

---

# Key Points

* NAT stands for Network Address Translation.
* NAT allows private resources to communicate with external networks.
* AWS provides NAT Gateway as a managed service.
* Private subnets commonly use NAT Gateway for outbound internet access.
* The private subnet's default route commonly points to the NAT Gateway.
* The NAT Gateway is normally placed in a public subnet.
* The public subnet needs a route to an Internet Gateway.
* NAT Gateway does not provide general unsolicited inbound access to private resources.
* NAT Gateway is a paid AWS service.
* Security Groups, NACLs, and route tables still affect connectivity.
* NAT Gateway placement across Availability Zones matters for resilient architectures.

---

## Cloud Engineer Focus

For your AWS career, remember this flow:

**Private EC2 → Private Route Table → NAT Gateway → Internet Gateway → Internet**

And understand the difference between:

**Internet Gateway = internet connectivity for the VPC**

**NAT Gateway = outbound internet access for private resources**

These concepts are directly relevant to AWS VPC and cloud support roles.
