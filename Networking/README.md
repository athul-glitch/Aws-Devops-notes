# Networking Notes

Practical networking notes focused on **cloud infrastructure, AWS, Linux, cloud support, and infrastructure engineering**.

These notes cover the networking fundamentals required to understand how systems communicate, how traffic is routed, how services are exposed, and how cloud networks are designed.

The focus is on **practical cloud networking**, rather than advanced enterprise networking or CCNP-level topics.

---

## Networking Fundamentals

* What is Computer Networking?
* Network Types — LAN, WAN, MAN
* Client-Server Model
* Network Devices
* Packets, Frames, and Segments
* MAC Addresses
* IP Addresses
* Public vs Private Networks

## Network Models

* OSI Model
* TCP/IP Model
* OSI vs TCP/IP
* Encapsulation and Decapsulation
* Understanding Data Flow Through Network Layers

## IP Addressing

* IPv4 Addressing
* IPv4 Address Structure
* Public and Private IPv4 Addresses
* Subnet Masks
* Default Gateway
* Network Address
* Broadcast Address
* Host Addresses
* CIDR Notation
* IPv4 Address Ranges

## Subnetting

* Why Subnetting Is Used
* Subnet Masks
* CIDR Calculation
* Network and Host Bits
* Number of Subnets
* Number of Hosts
* Subnetting Examples
* VLSM Basics
* Subnetting in Cloud Networks

## TCP, UDP, and Ports

* TCP
* UDP
* TCP vs UDP
* TCP 3-Way Handshake
* TCP Connection Termination
* Ports
* Sockets
* Well-Known Ports
* Common Application Ports

## Important Network Protocols

* ARP
* ICMP
* DHCP
* DNS
* HTTP
* HTTPS
* TLS Basics
* SSH
* FTP / SFTP
* SMTP

## DNS

* What is DNS?
* Domain Names
* DNS Resolution
* DNS Records
* A Record
* AAAA Record
* CNAME
* MX
* NS
* TXT
* TTL
* Recursive vs Authoritative DNS
* DNS Troubleshooting

## DHCP

* What is DHCP?
* DHCP DORA Process
* IP Address Allocation
* DHCP Lease
* Default Gateway
* DNS Configuration
* DHCP in Cloud Environments

## Routing

* What is Routing?
* Routing Tables
* Default Routes
* Static Routing
* Dynamic Routing — Concept
* Next Hop
* Route Selection
* Longest Prefix Match
* Default Gateway
* Internet Routing Basics

## NAT

* What is NAT?
* Why NAT Is Used
* SNAT
* DNAT
* PAT
* Public vs Private IP Communication
* NAT Gateway
* NAT in Cloud Networks

## HTTP and HTTPS

* HTTP Request and Response
* HTTP Methods
* HTTP Status Codes
* HTTP Headers
* HTTPS
* TLS Basics
* Certificates
* HTTP vs HTTPS
* How a Browser Reaches a Web Server

## Network Security

* Firewalls
* Stateful vs Stateless Filtering
* Inbound and Outbound Traffic
* Ports and Protocols
* Network Segmentation
* Security Groups
* Network ACLs
* Least Exposure
* Zero Trust Basics

## Cloud Networking

* Cloud Networking Fundamentals
* VPC
* VPC CIDR
* Subnets
* Availability Zones
* Public and Private Subnets
* Route Tables
* Internet Gateway
* NAT Gateway
* Security Groups
* Network ACLs
* VPC Endpoints
* VPC Peering
* Transit Gateway
* Load Balancers
* VPN
* Direct Connect — Concept
* Hybrid Networking

## AWS Traffic Flow

Understanding how traffic moves through AWS infrastructure:

```text
Internet
   |
   v
DNS
   |
   v
Load Balancer
   |
   v
Application Server
   |
   v
Database
```

And for private resources:

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

## Network Troubleshooting

Practical troubleshooting methodology:

```text
DNS
 ↓
IP Address
 ↓
Routing
 ↓
Security Rules
 ↓
Port
 ↓
Service
 ↓
Application
```

### Linux Networking Commands

```bash
ip addr
ip route
ping
traceroute
ss
curl
nslookup
dig
netstat
```

Topics include:

* Checking IP addresses
* Checking routing tables
* Testing connectivity
* Testing ports
* Checking DNS resolution
* Inspecting network connections
* Troubleshooting HTTP/HTTPS connectivity

## Cloud Networking Architecture

Practical architectures covered in these notes:

### Basic Web Application

```text
Internet
   |
   v
Public Subnet
   |
   v
EC2
```

### Three-Tier Architecture

```text
Internet
   |
   v
Load Balancer
   |
   v
Application Tier
   |
   v
Database Tier
```

### Highly Available Architecture

```text
                 Internet
                    |
                    v
              Load Balancer
               /          \
              v            v
          AZ-1 EC2      AZ-2 EC2
              \            /
               \          /
                v        v
                 Database
```

## Important Networking Comparisons

* TCP vs UDP
* MAC vs IP
* Public IP vs Private IP
* IPv4 vs IPv6
* OSI vs TCP/IP
* TCP vs HTTP
* HTTP vs HTTPS
* DNS vs DHCP
* Router vs Switch
* NAT vs Routing
* Security Group vs NACL
* Public Subnet vs Private Subnet
* Internet Gateway vs NAT Gateway
* VPC Peering vs Transit Gateway
* VPN vs Direct Connect

---

## Learning Goal

The goal of these notes is to build a strong networking foundation for:

* AWS
* Cloud Support
* Cloud Engineering
* Linux Administration
* Terraform
* Infrastructure Engineering
* DevOps

The focus is on understanding **how traffic moves from one system to another and how cloud infrastructure controls that traffic**.
