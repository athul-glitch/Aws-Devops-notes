# IP Addressing and CIDR

## Introduction

An IP address identifies a device or network interface on a network.

IP addressing is one of the most important networking concepts for cloud engineers because AWS VPCs, subnets, EC2 instances, route tables, and security rules all use IP addresses.

Important concepts:

* IPv4
* IPv6
* Public IP
* Private IP
* Network address
* Host address
* Subnet
* CIDR
* Default gateway

---

# 1. What is an IP Address?

An IP address is a logical address used to identify a device on a network.

Example:

```text
192.168.1.10
```

A computer can use an IP address to communicate with other devices.

For example:

```text
Client
192.168.1.10
     |
     | Network
     |
Server
192.168.1.20
```

---

# 2. IPv4

IPv4 is the most commonly used IP addressing system.

An IPv4 address contains **32 bits**.

It is written as four numbers separated by dots.

Example:

```text
192.168.1.10
```

Each number is called an **octet**.

Example:

```text
192 . 168 . 1 . 10
 |     |    |    |
Octet Octet Octet Octet
```

Each octet can have a value from:

```text
0 to 255
```

Therefore, a valid IPv4 address can look like:

```text
10.0.0.10
172.16.5.20
192.168.1.100
```

---

# 3. IPv6

IPv6 was created because the number of available IPv4 addresses is limited.

IPv6 uses **128 bits**.

Example:

```text
2001:db8:85a3::8a2e:370:7334
```

IPv6 addresses are much larger than IPv4 addresses.

For your current AWS/cloud preparation, focus more on IPv4 and CIDR first.

---

# 4. Public IP Address

A public IP address can be reachable over the Internet, depending on routing and security rules.

Example:

```text
3.110.25.50
```

A public IP may be used for resources that need Internet connectivity.

Example:

```text
Internet
    |
Public IP
    |
EC2 Instance
```

Important:

Having a public IP does **not automatically mean** that all traffic is allowed.

Security controls such as Security Groups, NACLs, and routing still matter.

---

# 5. Private IP Address

A private IP address is used inside a private network.

Common private IPv4 ranges are:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

Examples:

```text
10.0.1.10
172.16.5.20
192.168.1.50
```

Private IP addresses are commonly used inside:

* VPCs
* Subnets
* Internal networks
* Private servers

---

# 6. Public vs Private IP

| Public IP                                    | Private IP                          |
| -------------------------------------------- | ----------------------------------- |
| Used for Internet-facing communication       | Used inside private networks        |
| Globally routable                            | Not directly Internet-routable      |
| Can be assigned to Internet-facing resources | Commonly used by internal resources |
| Example: `3.110.25.50`                       | Example: `10.0.1.10`                |

### AWS Example

An EC2 instance could have:

```text
Private IP:
10.0.1.10

Public IP:
3.110.25.50
```

The private IP is used inside the VPC.

The public IP can be used for Internet communication.

---

# 7. Network and Host

An IP address can be divided into two logical parts:

```text
Network portion + Host portion
```

The **network portion** identifies the network.

The **host portion** identifies a device within that network.

Example:

```text
192.168.1.10/24
```

Here:

```text
Network: 192.168.1.0
Host:    10
```

The exact division depends on the subnet mask or CIDR prefix.

---

# 8. What is a Subnet?

A subnet is a smaller network created from a larger IP network.

For example:

```text
VPC
10.0.0.0/16
```

can contain smaller subnets:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

AWS uses subnets to organize resources within a VPC.

Example:

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

---

# 9. What is CIDR?

CIDR stands for:

**Classless Inter-Domain Routing**

CIDR is written using a slash followed by a number.

Example:

```text
10.0.0.0/16
```

The `/16` indicates how many bits are used for the network portion.

Another example:

```text
10.0.1.0/24
```

Here `/24` means the first 24 bits represent the network portion.

---

# 10. Common CIDR Sizes

Some common CIDR ranges are:

| CIDR | Total IPv4 Addresses |
| ---- | -------------------: |
| /8   |           16,777,216 |
| /16  |               65,536 |
| /20  |                4,096 |
| /24  |                  256 |
| /25  |                  128 |
| /26  |                   64 |
| /27  |                   32 |
| /28  |                   16 |
| /30  |                    4 |

The larger the prefix number, the smaller the network.

For example:

```text
/16 → Larger network
/24 → Smaller network
/28 → Much smaller network
```

---

# 11. CIDR Example

Consider:

```text
192.168.1.0/24
```

A `/24` network contains:

```text
256 total IPv4 addresses
```

The range is:

```text
192.168.1.0
to
192.168.1.255
```

In a normal IPv4 network:

```text
Network address:
192.168.1.0

Usable host range:
192.168.1.1 - 192.168.1.254

Broadcast address:
192.168.1.255
```

---

# 12. CIDR and AWS

CIDR is extremely important in AWS.

When creating a VPC, you choose a CIDR block.

Example:

```text
VPC CIDR:
10.0.0.0/16
```

Then you can create subnets:

```text
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

Another subnet could be:

```text
10.0.3.0/24
```

These subnets are part of the VPC network.

---

# 13. AWS VPC Example

A simple VPC could look like:

```text
VPC
10.0.0.0/16
       |
       +----------------------+
       |                      |
Public Subnet            Private Subnet
10.0.1.0/24              10.0.2.0/24
       |                      |
     EC2                    Database
```

The VPC provides the overall network.

The subnets divide the VPC into smaller networks.

---

# 14. Default Gateway

A default gateway is the device used to send traffic outside the local network.

Example:

```text
Computer
192.168.1.10
      |
      |
Gateway
192.168.1.1
      |
   Internet
```

If the destination is outside the local network, the device sends the traffic to the default gateway.

### AWS

In AWS, routing is handled using route tables and gateways such as:

* Internet Gateway
* NAT Gateway

---

# 15. Network Address

The network address identifies the network itself.

Example:

```text
192.168.1.0/24
```

The network address is:

```text
192.168.1.0
```

It represents the entire network.

---

# 16. Broadcast Address

In traditional IPv4 networks, the broadcast address is used to communicate with all hosts in the local network.

For:

```text
192.168.1.0/24
```

the broadcast address is:

```text
192.168.1.255
```

However, AWS VPC networking does not support traditional IPv4 broadcast and multicast traffic in the same way as a normal LAN.

For AWS work, focus mainly on:

* Network address
* CIDR
* Subnets
* Usable IP addresses
* Routing

---

# 17. AWS Reserved IP Addresses

AWS reserves some IP addresses in every subnet.

For example:

```text
10.0.1.0/24
```

has 256 total IPv4 addresses.

AWS reserves **5 IP addresses** in each subnet.

Therefore:

```text
256 total
- 5 reserved
= 251 usable
```

This is important when calculating how many IPv4 addresses are available for AWS resources.

---

# 18. CIDR and Subnet Planning

When designing an AWS VPC, choose CIDR ranges carefully.

Example:

```text
VPC:
10.0.0.0/16
```

Subnets:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

This gives separate IP ranges for different subnets.

Good IP planning helps avoid:

* IP address conflicts
* Overlapping networks
* Difficult future expansion

---

# 19. CIDR Overlap

Two networks should not overlap when they need to communicate or connect.

Example:

```text
Network A:
10.0.0.0/16

Network B:
10.0.0.0/16
```

These networks overlap.

This can create problems when connecting networks.

### Cloud relevance

CIDR overlap is important when working with:

* VPC Peering
* VPN connections
* Transit Gateway
* Hybrid cloud
* VPC migrations

---

# 20. Private IP Example in AWS

Suppose an EC2 instance has:

```text
Private IP:
10.0.1.25
```

and belongs to:

```text
Subnet:
10.0.1.0/24
```

The instance communicates with other resources inside the VPC using its private IP.

For example:

```text
EC2
10.0.1.25
   |
   | Private network
   |
RDS
10.0.2.50
```

The resources can communicate if routing and security rules allow the traffic.

---

# 21. Useful Linux Commands

### Display IP addresses

```bash
ip addr
```

### Display routing table

```bash
ip route
```

### Check a specific IP

```bash
ip addr show
```

### Test connectivity

```bash
ping 8.8.8.8
```

### Check DNS resolution

```bash
nslookup example.com
```

---

# Key Points

* An IP address identifies a device or network interface.
* IPv4 uses **32 bits**.
* IPv6 uses **128 bits**.
* Public IPs are used for Internet-facing communication.
* Private IPs are used inside private networks.
* Private IPv4 ranges include:

  * `10.0.0.0/8`
  * `172.16.0.0/12`
  * `192.168.0.0/16`
* A subnet is a smaller network inside a larger network.
* CIDR represents an IP network using a prefix such as `/16` or `/24`.
* A `/16` network is larger than a `/24` network.
* AWS VPCs use CIDR blocks.
* AWS subnets use CIDR blocks.
* AWS reserves 5 IPv4 addresses in every subnet.
* CIDR planning is important for AWS network design.
* Avoid overlapping CIDR ranges when connecting networks.

## Cloud Engineer Focus

For AWS and cloud support roles, understand these especially well:

```text
IPv4
Private IP
Public IP
CIDR
Subnet
VPC CIDR
/16
/24
Usable IP addresses
AWS reserved IPs
CIDR overlap
Default gateway
```

The main goal is to be able to look at something like:

```text
VPC:    10.0.0.0/16
Public: 10.0.1.0/24
Private:10.0.2.0/24
```

and understand **what each network represents and how the IP ranges are organized**.
