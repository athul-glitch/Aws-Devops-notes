# NAT Gateway

## What is a NAT Gateway?

A **NAT Gateway (Network Address Translation Gateway)** allows resources in a **private subnet** to access the internet for outbound communication without allowing the internet to directly initiate connections to those resources.

NAT stands for **Network Address Translation**.

Typical example:

```text
Internet
    |
    v
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

The private EC2 instance can access the internet through the NAT Gateway, but external systems cannot directly initiate a connection to that EC2 instance through the NAT Gateway.

---

# Why NAT Gateway is Used

A private-subnet server may need outbound internet access for tasks such as:

* Downloading software updates
* Installing packages
* Accessing public APIs
* Downloading application dependencies
* Connecting to external services

But the server should not be directly exposed to the internet.

NAT Gateway provides this pattern:

```text
Private EC2
     |
     v
NAT Gateway
     |
     v
Internet
```

---

# Public vs Private Subnet

The main difference is the subnet's route to the Internet Gateway.

### Public subnet

A subnet is considered public when its route table has a route to an Internet Gateway.

Example:

```text
0.0.0.0/0
      |
      v
Internet Gateway
```

### Private subnet

A private subnet does not have a direct route to the Internet Gateway.

Instead, outbound internet traffic can be routed through a NAT Gateway.

```text
0.0.0.0/0
      |
      v
NAT Gateway
```

---

# NAT Gateway Architecture

A common VPC architecture looks like:

```text
                    Internet
                       |
                       v
                Internet Gateway
                       |
        +--------------+--------------+
        |                             |
 Public Subnet                  Public Subnet
        |                             |
   NAT Gateway                  NAT Gateway
        |                             |
 Private Subnet                Private Subnet
        |                             |
      EC2                           EC2
```

The NAT Gateways are placed in **public subnets**.

Private resources use the NAT Gateway for outbound internet access.

---

# Why is NAT Gateway in a Public Subnet?

A NAT Gateway needs access to the Internet Gateway.

Therefore, it is normally deployed in a public subnet.

Example:

```text
Public Subnet
|
+-- NAT Gateway
|
+-- Route to Internet Gateway
```

Private EC2 instances then route internet-bound traffic to the NAT Gateway.

---

# Route Tables

Route tables determine where network traffic is sent.

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

The important difference is the default route:

```text
Public subnet
0.0.0.0/0 → Internet Gateway

Private subnet
0.0.0.0/0 → NAT Gateway
```

---

# Example

Suppose an EC2 instance is located in:

```text
Private subnet
10.0.2.0/24
```

It needs to download a package from the internet.

Traffic flows like:

```text
EC2
 |
 | Destination: Internet
 v
Private Route Table
 |
 | 0.0.0.0/0
 v
NAT Gateway
 |
 v
Internet Gateway
 |
 v
Internet
```

The response returns through the NAT Gateway to the private EC2 instance.

---

# NAT Gateway vs Internet Gateway

These two services have different purposes.

| Feature                         | Internet Gateway | NAT Gateway                       |
| ------------------------------- | ---------------- | --------------------------------- |
| VPC component                   | Yes              | Yes                               |
| Provides internet connectivity  | Yes              | Indirectly                        |
| Used by public resources        | Yes              | No                                |
| Used by private resources       | No               | Yes                               |
| Requires public IP for resource | Usually          | Private resource doesn't need one |
| Stateful translation            | No               | Yes                               |
| Managed by AWS                  | Yes              | Yes                               |

Simple rule:

```text
Public subnet → Internet Gateway

Private subnet → NAT Gateway → Internet Gateway
```

---

# NAT Gateway vs NAT Instance

AWS also historically supported NAT Instances.

### NAT Gateway

* Managed by AWS
* Highly available within its Availability Zone
* Scales automatically
* Less operational management
* Recommended for most modern architectures

### NAT Instance

* EC2 instance configured for NAT
* Requires management
* Requires scaling and maintenance
* Can become a bottleneck
* More operational work

For most SAA scenarios, **NAT Gateway** is the preferred managed solution.

---

# NAT Gateway and Public IP

A public NAT Gateway uses an **Elastic IP address**.

Example:

```text
Private EC2
     |
     v
NAT Gateway
     |
 Elastic IP
     |
     v
Internet Gateway
     |
     v
Internet
```

The external service sees the NAT Gateway's public IP rather than the private EC2 instance's private IP.

---

# NAT Gateway is Outbound Only

NAT Gateway is primarily used for outbound connections from private resources.

Example:

```text
Private EC2
     |
     | Request
     v
NAT Gateway
     |
     v
Internet
```

The internet cannot simply initiate a new connection to the private EC2 instance through the NAT Gateway.

This helps keep private resources from being directly exposed.

---

# NAT Gateway and Security Groups

A NAT Gateway does not replace security groups.

For example:

```text
Private EC2
   |
Security Group
   |
NAT Gateway
   |
Internet
```

The EC2 security group still controls the instance's network traffic.

Security Groups are **stateful**.

---

# NAT Gateway and Network ACLs

Network ACLs also affect traffic.

A typical traffic path may involve:

```text
EC2
 ↓
Security Group
 ↓
Subnet Network ACL
 ↓
NAT Gateway
 ↓
Internet Gateway
 ↓
Internet
```

If connectivity fails, check:

* Route tables
* Security Groups
* Network ACLs
* NAT Gateway
* Internet Gateway
* DNS configuration

---

# NAT Gateway Availability

A NAT Gateway is associated with a specific Availability Zone.

For a highly available architecture, use a NAT Gateway in each AZ where private workloads need independent outbound connectivity.

Example:

```text
                 VPC
                  |
        +---------+---------+
        |                   |
       AZ-A                AZ-B
        |                   |
 Public Subnet          Public Subnet
        |                   |
 NAT Gateway A          NAT Gateway B
        |                   |
 Private Subnet         Private Subnet
        |                   |
     EC2-A                 EC2-B
```

Each private subnet can use the NAT Gateway in its own Availability Zone.

---

# Why One NAT Gateway Can Be a Problem

Consider:

```text
AZ-A
 |
NAT Gateway
 |
Private EC2

AZ-B
 |
Private EC2
 |
NAT Gateway in AZ-A
```

If AZ-A becomes unavailable, the private resources in AZ-B may lose their normal outbound internet path.

There can also be cross-AZ data transfer considerations.

For production high availability, deploying NAT Gateways per AZ is commonly preferred.

---

# Cost Consideration

NAT Gateway is a **paid AWS service**.

Costs can include:

* NAT Gateway hourly charges
* Data processing charges
* Cross-AZ data transfer in certain architectures

Therefore, avoid creating NAT Gateways unnecessarily in small learning environments.

For AWS learning accounts with limited credits, NAT Gateway usage should be monitored carefully.

---

# NAT Gateway vs VPC Endpoints

Sometimes a private resource does not need general internet access.

For example, an EC2 instance may only need to access S3.

Instead of:

```text
EC2
 |
NAT Gateway
 |
Internet Gateway
 |
S3
```

you can use a VPC endpoint:

```text
EC2
 |
VPC Endpoint
 |
S3
```

This can reduce NAT Gateway dependency and potentially reduce costs.

---

# Gateway Endpoint

AWS provides Gateway VPC Endpoints for:

* Amazon S3
* DynamoDB

Example:

```text
Private EC2
     |
     v
Gateway Endpoint
     |
     v
S3
```

This allows private resources to access supported AWS services without requiring a NAT Gateway for that traffic.

---

# Interface VPC Endpoints

Interface endpoints use **AWS PrivateLink** and create network interfaces inside your subnets.

They can provide private connectivity to many AWS services and supported applications.

Example:

```text
Private EC2
     |
     v
Interface Endpoint
     |
     v
AWS Service
```

This is useful when private resources need private connectivity to services without traversing the public internet.

---

# NAT Gateway vs VPC Endpoint

| Feature                                  | NAT Gateway                      | VPC Endpoint               |
| ---------------------------------------- | -------------------------------- | -------------------------- |
| General internet access                  | Yes                              | No                         |
| Private access to supported AWS services | Not specifically                 | Yes                        |
| Used by private subnets                  | Yes                              | Yes                        |
| Requires Internet Gateway path           | Yes, for internet-bound traffic  | No                         |
| Common use                               | Package downloads, external APIs | Private AWS service access |

---

# NAT Gateway in a Three-Tier Architecture

A common architecture is:

```text
                 Internet
                    |
                    v
             Internet Gateway
                    |
               Public Subnet
                    |
                   ALB
                    |
          +---------+---------+
          |                   |
      Private Subnet      Private Subnet
          |                   |
         EC2                 EC2
          |                   |
          +---------+---------+
                    |
               NAT Gateway
                    |
                    v
                 Internet
```

The application servers remain in private subnets while still being able to make outbound connections.

---

# NAT Gateway and Load Balancer

A NAT Gateway is **not** a replacement for a load balancer.

They solve different problems.

### NAT Gateway

Provides outbound internet connectivity for private resources.

### Application Load Balancer

Distributes incoming application traffic across targets.

```text
Incoming traffic
      |
      v
     ALB
      |
   EC2 EC2
```

while:

```text
EC2
 |
 v
NAT Gateway
 |
 v
Internet
```

handles outbound traffic.

---

# Common SAA Scenario

### Requirement

A company has EC2 instances in private subnets.

The instances need to download software updates from the internet, but they must not have public IP addresses.

### Solution

Use:

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

This is a classic NAT Gateway use case.

---

# Another SAA Scenario

### Requirement

A private EC2 instance needs access to S3 but should not access the public internet.

### Better solution

Use an **S3 VPC Gateway Endpoint**.

```text
EC2
 |
VPC Endpoint
 |
S3
```

A NAT Gateway is unnecessary for S3 traffic in this case.

---

# Another SAA Scenario

### Requirement

A company has private application servers in two Availability Zones and wants high availability for outbound internet access.

### Suitable architecture

```text
AZ-A                     AZ-B
 |                        |
Private EC2             Private EC2
 |                        |
NAT Gateway A            NAT Gateway B
 |                        |
 +-----------+------------+
             |
        Internet Gateway
             |
          Internet
```

Using a NAT Gateway per AZ avoids making one AZ's NAT Gateway a single point of dependency for the other AZ.

---

# Troubleshooting NAT Gateway Connectivity

If a private EC2 instance cannot access the internet, check the following.

### 1. NAT Gateway

Confirm the NAT Gateway exists and is available.

### 2. NAT Gateway subnet

Confirm the NAT Gateway is deployed in a public subnet.

### 3. Elastic IP

Confirm the public NAT Gateway has an Elastic IP.

### 4. Public route table

Confirm the NAT Gateway's subnet has:

```text
0.0.0.0/0 → Internet Gateway
```

### 5. Private route table

Confirm the private subnet has:

```text
0.0.0.0/0 → NAT Gateway
```

### 6. Security Group

Check outbound rules on the EC2 security group.

### 7. Network ACL

Check whether the subnet NACL is blocking the traffic.

### 8. DNS

If the instance can reach an IP address but cannot resolve domain names, investigate DNS configuration.

---

# NAT Gateway with Terraform

NAT Gateway infrastructure can be created using Terraform.

Typical resources include:

```text
VPC
Internet Gateway
Public Subnet
Private Subnet
Elastic IP
NAT Gateway
Public Route Table
Private Route Table
Route Table Associations
```

Typical architecture:

```text
VPC
 |
+-- Internet Gateway
|
+-- Public Subnet
|      |
|     NAT Gateway
|
+-- Private Subnet
       |
      EC2
```

Terraform makes the networking configuration reproducible and easier to manage.

---

# Sheepeye Relevance

NAT Gateway can be useful if Sheepeye's EC2 instance is moved into a **private subnet**.

For example:

```text
Internet
   |
  ALB
   |
Private EC2
   |
NAT Gateway
   |
Internet
```

The EC2 instance could remain private while still accessing:

* Package repositories
* External APIs
* Software updates
* Other internet services

However, NAT Gateway has a cost.

Since the current Sheepeye project is cost-conscious, do not add a NAT Gateway just for the sake of adding AWS services.

First understand the architecture and add it only when the private-subnet design actually requires outbound internet access.

---

# Key Takeaways

* NAT Gateway provides outbound internet access for private-subnet resources.
* NAT Gateway is normally deployed in a public subnet.
* NAT Gateway uses an Elastic IP for public internet communication.
* Private subnet route tables point internet-bound traffic to the NAT Gateway.
* The NAT Gateway uses the Internet Gateway to reach the internet.
* NAT Gateway does not make the private EC2 instance directly reachable from the internet.
* NAT Gateway is different from an Internet Gateway.
* NAT Gateway is different from a Load Balancer.
* NAT Gateway is a managed AWS service.
* NAT Gateway is charged for usage.
* For high availability, use NAT Gateways in multiple Availability Zones.
* VPC Endpoints can provide private access to supported AWS services without using NAT for that traffic.
* S3 and DynamoDB support Gateway VPC Endpoints.
* NAT Gateway is a common AWS Solutions Architect exam scenario.
* Always check route tables when troubleshooting private-subnet internet connectivity.
