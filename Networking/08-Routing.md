# Routing

## Introduction

**Routing** is the process of deciding where network traffic should go.

When a device wants to communicate with another network, a router or routing system determines the path the packets should take.

Routing is extremely important in cloud environments, especially in **AWS VPCs**.

---

# 1. What is a Router?

A **router** connects different networks and forwards packets between them.

Example:

```text
Network A
   |
   v
 Router
   |
   v
Network B
```

A router checks the destination IP address and decides where to send the packet.

---

# 2. What is a Route?

A **route** tells the network where traffic for a particular destination should be sent.

A simple route can look like:

```text
Destination       Next Hop
192.168.2.0/24    Router
```

This means:

> To reach the `192.168.2.0/24` network, send the traffic to the specified router/next hop.

---

# 3. Routing Table

A **routing table** contains routes used to determine where packets should go.

Example:

```text
Destination       Next Hop
10.0.0.0/16       Local
0.0.0.0/0         Internet Gateway
```

The routing system checks the destination IP against these routes.

---

# 4. Default Route

A **default route** is used when there is no more specific route available.

The IPv4 default route is:

```text
0.0.0.0/0
```

It means:

> Any IPv4 destination that does not match another route.

Example:

```text
Destination
0.0.0.0/0
     |
     v
Internet Gateway
```

---

# 5. Longest Prefix Match

When multiple routes match a destination, the most specific route is normally selected.

Example:

```text
10.0.0.0/8
10.0.1.0/24
```

For traffic destined for:

```text
10.0.1.50
```

the `/24` route is more specific than the `/8` route.

Therefore, the `/24` route is selected.

This is called **longest prefix match**.

---

# 6. Routing Example

Suppose a computer has:

```text
IP: 192.168.1.10
```

and wants to reach:

```text
192.168.2.20
```

The computer checks its routing table.

```text
Computer
   |
   | Destination: 192.168.2.20
   v
Routing Table
   |
   v
Router
   |
   v
192.168.2.20
```

The router then forwards the packet toward the destination network.

---

# 7. Default Gateway

A **default gateway** is the device used to reach networks outside the local network when no more specific route exists.

Example:

```text
Laptop
   |
   | 192.168.1.10
   v
Default Gateway
192.168.1.1
   |
   v
Internet
```

The default gateway is usually a router.

---

# 8. Static Routing

A **static route** is manually configured by an administrator.

Example:

```text
Destination: 10.10.0.0/16
Next Hop: 192.168.1.1
```

Static routing is simple and predictable, but manually managing many routes can become difficult.

---

# 9. Dynamic Routing

Dynamic routing uses routing protocols to learn and update routes automatically.

Examples include:

* OSPF
* BGP
* EIGRP

For your current AWS/cloud preparation, you don't need deep routing-protocol configuration.

The important concept is:

> Routing determines where packets should go.

---

# 10. Routing in AWS

AWS uses **route tables** inside VPCs to control where network traffic is directed.

A subnet is associated with a route table.

Example:

```text
VPC
 |
 +---- Public Subnet
 |        |
 |        +---- Route Table
 |
 +---- Private Subnet
          |
          +---- Route Table
```

---

# 11. AWS Route Table Example

A public subnet might have:

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Meaning:

* Traffic inside the VPC uses the local route.
* Other IPv4 traffic is sent to the Internet Gateway.

---

# 12. Public Subnet Routing

A typical public subnet has a route to an **Internet Gateway**.

Example:

```text
Internet
   |
   v
Internet Gateway
   |
   v
Route Table
   |
   v
Public Subnet
   |
   v
EC2
```

A route such as:

```text
0.0.0.0/0 → Internet Gateway
```

is part of what makes the subnet publicly routed.

However, routing alone does not automatically make an EC2 instance publicly accessible. Other requirements, such as a public IPv4 address and appropriate Security Group/NACL rules, also matter.

---

# 13. Private Subnet Routing

A private subnet normally does not have a direct route from the subnet to an Internet Gateway for general outbound internet access.

For outbound internet access, a common design is:

```text
Private EC2
    |
    v
Private Route Table
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

The private subnet might have:

```text
0.0.0.0/0 → NAT Gateway
```

---

# 14. Local Route

AWS VPC route tables contain a local route for communication within the VPC CIDR.

For example, if the VPC is:

```text
10.0.0.0/16
```

the route table includes a route similar to:

```text
10.0.0.0/16 → local
```

This allows resources in the VPC to communicate according to the VPC's networking configuration.

---

# 15. Route Tables and Subnets

Different subnets can use different route tables.

Example:

```text
VPC
 |
 +-----------------------+
 |                       |
 v                       v
Public Subnet        Private Subnet
 |                       |
 v                       v
Public RT             Private RT
 |                       |
 | 0.0.0.0/0             | 0.0.0.0/0
 | → IGW                  | → NAT Gateway
```

This is one of the fundamental patterns in AWS networking.

---

# 16. Internet Gateway

An **Internet Gateway (IGW)** allows communication between a VPC and the internet when the required routing and addressing rules are configured.

Typical public subnet:

```text
EC2
 |
 v
Route Table
 |
 | 0.0.0.0/0
 v
Internet Gateway
 |
 v
Internet
```

---

# 17. NAT Gateway

A **NAT Gateway** allows resources in a private subnet to initiate connections to external destinations, such as the internet, without requiring those resources to have public IP addresses.

Example:

```text
Private EC2
     |
     v
Private Route Table
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

NAT is mainly used for **outbound** connectivity from private resources.

---

# 18. Route Tables and Security Groups

Routing and security are different things.

### Route Table

Determines:

> Where should the traffic go?

### Security Group

Determines:

> Is this traffic allowed to the resource?

Example:

```text
Client
  |
  v
Route Table
  |
  v
EC2
  |
  v
Security Group
```

Both routing and security rules may need to be correct for communication to work.

---

# 19. Routing Troubleshooting

If an EC2 instance cannot reach another resource, check:

### 1. Route Table

Is there a route to the destination?

```text
0.0.0.0/0 → NAT Gateway
```

or:

```text
10.0.0.0/16 → local
```

### 2. Subnet Association

Is the subnet associated with the expected route table?

### 3. Internet Gateway

For public internet access, is the VPC connected to an Internet Gateway?

### 4. NAT Gateway

For private-subnet outbound internet access, is the route pointing to a working NAT Gateway?

### 5. Security Group

Is the required traffic allowed?

### 6. Network ACL

Could the subnet-level NACL be blocking traffic?

### 7. IP Address

Is the destination IP correct?

---

# 20. Linux Routing Commands

### Show routing table

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0
```

### Show IP addresses

```bash
ip addr
```

### Test connectivity

```bash
ping 8.8.8.8
```

### Trace the path

```bash
traceroute example.com
```

If `traceroute` is not installed, it may need to be installed separately.

---

# 21. Simple AWS Architecture

A common AWS architecture looks like:

```text
                 Internet
                    |
                    v
             Internet Gateway
                    |
        +-----------+-----------+
        |                       |
        v                       v
 Public Subnet            Private Subnet
        |                       |
        v                       v
       EC2                  EC2 / App
                                |
                                v
                           NAT Gateway
```

The actual architecture can vary depending on the application.

---

# 22. Routing vs Switching

| Routing                        | Switching                         |
| ------------------------------ | --------------------------------- |
| Connects different networks    | Connects devices within a network |
| Uses IP addresses              | Primarily uses MAC addresses      |
| Usually performed by routers   | Usually performed by switches     |
| Important for VPC connectivity | Important for local networks      |

For cloud engineering, routing is especially important because AWS networking relies heavily on route tables and network paths.

---

# Key Points

* Routing determines where packets should go.
* A route contains a destination and a target/next hop.
* A routing table contains multiple routes.
* `0.0.0.0/0` is the IPv4 default route.
* More specific routes take priority over less specific routes.
* AWS subnets use route tables.
* Public subnets commonly route internet traffic through an Internet Gateway.
* Private subnets commonly use a NAT Gateway for outbound internet access.
* Route tables and Security Groups have different purposes.
* Routing is one of the most important AWS networking fundamentals.

---

## Cloud Engineer Focus

For your AWS career, focus strongly on:

**Route Tables → Local Route → Default Route → Internet Gateway → NAT Gateway → Public/Private Subnets → Routing Troubleshooting**

These concepts are directly useful for AWS VPC work and cloud support roles.
