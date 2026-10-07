# OSI and TCP/IP Models

## Introduction

The OSI and TCP/IP models help us understand how devices communicate over a network.

They divide network communication into layers, making it easier to understand and troubleshoot network problems.

For cloud engineers, these models are useful for understanding:

* IP addresses
* TCP and UDP
* Ports
* DNS
* HTTP/HTTPS
* Routing
* Network security

---

# 1. OSI Model

OSI stands for **Open Systems Interconnection**.

The OSI model has **7 layers**.

| Layer | Name         | Main Purpose                          | Examples              |
| ----: | ------------ | ------------------------------------- | --------------------- |
|     7 | Application  | Network services used by applications | HTTP, HTTPS, DNS, SSH |
|     6 | Presentation | Data format and encryption            | TLS, encoding         |
|     5 | Session      | Manages communication sessions        | Session management    |
|     4 | Transport    | Reliable delivery and ports           | TCP, UDP              |
|     3 | Network      | IP addressing and routing             | IP, routers           |
|     2 | Data Link    | Local network communication           | MAC, Ethernet         |
|     1 | Physical     | Sends signals                         | Cables, Wi-Fi, NIC    |

### Easy way to remember

**Application → Presentation → Session → Transport → Network → Data Link → Physical**

---

# 2. Layer 7 — Application

The Application layer is where network services interact with applications.

Examples:

* HTTP
* HTTPS
* DNS
* SSH
* SMTP
* FTP

### Example

When you open:

```text
https://example.com
```

the browser uses HTTPS to communicate with the web server.

### Cloud relevance

Cloud applications commonly use:

* HTTPS for websites
* SSH for Linux administration
* DNS for domain resolution
* HTTP/HTTPS for APIs

---

# 3. Layer 6 — Presentation

The Presentation layer deals with how data is represented.

It can involve:

* Data formatting
* Encoding
* Encryption
* Compression

### Example

HTTPS uses **TLS encryption** to protect data during communication.

For a junior cloud engineer, you don't need deep knowledge of this layer. Understanding basic encryption is enough.

---

# 4. Layer 5 — Session

The Session layer manages communication sessions between applications.

It can handle:

* Starting a session
* Maintaining a session
* Ending a session

In modern networks, these functions are often handled by applications and protocols rather than by a separate dedicated layer.

---

# 5. Layer 4 — Transport

The Transport layer manages communication between applications.

The two main protocols are:

* TCP
* UDP

It also uses **port numbers** to identify services.

## TCP

TCP provides reliable communication.

Features:

* Connection-oriented
* Reliable delivery
* Error checking
* Ordered data delivery

Common examples:

```text
HTTP/HTTPS
SSH
SMTP
```

## UDP

UDP is lightweight and has less overhead than TCP.

It does not establish a connection in the same way TCP does.

Common examples:

```text
DNS
DHCP
Streaming
Online gaming
```

## Common Ports

| Port | Protocol   | Common Use             |
| ---: | ---------- | ---------------------- |
|   22 | SSH        | Linux remote access    |
|   53 | DNS        | Domain name resolution |
|   80 | HTTP       | Web traffic            |
|  443 | HTTPS      | Secure web traffic     |
|   25 | SMTP       | Email                  |
| 3306 | MySQL      | MySQL database         |
| 5432 | PostgreSQL | PostgreSQL database    |

### Cloud relevance

AWS Security Groups can control traffic based on:

* Protocol
* Port
* Source/destination

Example:

```text
Allow TCP 443
Source: 0.0.0.0/0
```

This allows HTTPS traffic to the resource.

---

# 6. Layer 3 — Network

The Network layer handles:

* IP addresses
* Routing
* Packets

Routers use IP addresses to determine where traffic should go.

### Example

A client:

```text
192.168.1.10
```

wants to communicate with:

```text
10.0.1.20
```

The network layer is responsible for addressing and routing the packets toward the destination.

### Cloud relevance

AWS VPC networking uses many Layer 3 concepts:

* IPv4
* IPv6
* CIDR
* Subnets
* Route tables
* Routing
* Internet Gateway
* NAT Gateway

---

# 7. Layer 2 — Data Link

The Data Link layer handles communication within a local network.

An important concept is the **MAC address**.

Example:

```text
00:1A:2B:3C:4D:5E
```

Ethernet operates primarily at this layer.

### Cloud relevance

You normally don't manage physical MAC addresses directly in AWS, but understanding MAC addresses helps explain how local network communication works.

---

# 8. Layer 1 — Physical

The Physical layer deals with the actual transmission of signals.

Examples:

* Network cables
* Fiber cables
* Radio signals
* Wi-Fi
* Network hardware

In cloud environments, the physical infrastructure is managed by the cloud provider.

Cloud engineers mainly work with the logical networking above this layer.

---

# 9. TCP/IP Model

The TCP/IP model is the practical networking model used by the Internet.

It commonly has **4 layers**.

| TCP/IP Layer   | Main Purpose                             |
| -------------- | ---------------------------------------- |
| Application    | Application protocols                    |
| Transport      | TCP/UDP and ports                        |
| Internet       | IP addressing and routing                |
| Network Access | Local network and physical communication |

---

# 10. OSI vs TCP/IP

The models can be roughly mapped like this:

| OSI Model    | TCP/IP Model   |
| ------------ | -------------- |
| Application  | Application    |
| Presentation | Application    |
| Session      | Application    |
| Transport    | Transport      |
| Network      | Internet       |
| Data Link    | Network Access |
| Physical     | Network Access |

The TCP/IP model combines some of the OSI layers.

### For Cloud Engineers

You mainly need to understand:

```text
Application → HTTP/HTTPS/DNS
Transport → TCP/UDP/Ports
Network → IP/Routing
Data Link → MAC/Ethernet
Physical → Network hardware
```

---

# 11. Encapsulation

When data is sent across a network, each layer adds information required for communication.

Simple flow:

```text
Application Data
       ↓
Transport → Segment
       ↓
Network → Packet
       ↓
Data Link → Frame
       ↓
Physical → Bits
```

Example:

```text
Browser
   ↓
HTTPS data
   ↓
TCP segment
   ↓
IP packet
   ↓
Ethernet frame
   ↓
Network transmission
```

---

# 12. Decapsulation

When the destination receives the data, the process happens in reverse.

```text
Bits
  ↓
Frame
  ↓
Packet
  ↓
Segment
  ↓
Application Data
```

The receiving system removes the information added by the different layers.

---

# 13. Example — Opening a Website

Suppose you open:

```text
https://sheepeye.shop
```

A simplified communication process is:

### Step 1 — DNS

The computer needs to find the IP address of the website.

```text
sheepeye.shop → IP address
```

### Step 2 — Transport

The client communicates with the destination using TCP.

For HTTPS:

```text
TCP → Port 443
```

### Step 3 — Routing

Packets travel through networks toward the destination.

Routers use IP addresses and routing information.

### Step 4 — HTTPS

The browser sends an HTTPS request to the web server.

### Step 5 — Response

The server processes the request and sends the response back.

---

# 14. Troubleshooting Using the OSI Model

The OSI model is useful for troubleshooting network problems.

## Application Problem

Example:

```text
Website returns an error
```

Check:

* Application
* HTTP/HTTPS
* DNS
* Server configuration

## Transport Problem

Example:

```text
Connection refused
```

Check:

* TCP/UDP
* Port
* Service status
* Firewall
* Security Group

## Network Problem

Example:

```text
Cannot reach the server
```

Check:

* IP address
* Route
* Subnet
* Route table
* Gateway

## Data Link Problem

Example:

```text
Local network communication problem
```

Check:

* Network interface
* MAC address
* Ethernet/Wi-Fi

## Physical Problem

Example:

```text
No network connection
```

Check:

* Cable
* Network hardware
* Wi-Fi
* Network interface

---

# 15. OSI Model and AWS

AWS networking can be understood using these basic layers.

### Application

Examples:

```text
HTTP
HTTPS
DNS
SSH
```

### Transport

Examples:

```text
TCP
UDP
Ports
```

Security Groups and NACLs can control network traffic based on protocols and ports.

### Network

Examples:

```text
IP
CIDR
Subnets
Routing
Route Tables
```

These are fundamental VPC concepts.

### Data Link and Physical

AWS manages the underlying physical infrastructure.

Customers mainly work with the logical networking provided by AWS.

---

# 16. Useful Linux Commands

### Check IP address

```bash
ip addr
```

### Check routing table

```bash
ip route
```

### Test connectivity

```bash
ping 8.8.8.8
```

### Test HTTP/HTTPS

```bash
curl https://example.com
```

### Check listening ports

```bash
ss -tuln
```

### Test DNS

```bash
nslookup example.com
```

or:

```bash
dig example.com
```

---

# Key Points

* OSI has **7 layers**.
* TCP/IP commonly has **4 layers**.
* TCP and UDP operate at the Transport layer.
* IP addressing and routing are associated with the Network layer.
* MAC addresses are associated with the Data Link layer.
* Ports identify network services.
* HTTP and HTTPS are Application layer protocols.
* TCP provides reliable communication.
* UDP is lightweight and connectionless.
* Routing uses IP addresses to move packets between networks.
* AWS VPC networking uses IP addressing, subnets, routing and security.
* The OSI model is useful for troubleshooting network problems.

## Cloud Engineer Focus

For AWS and cloud support roles, focus strongly on:

```text
IP Addressing
CIDR
Subnets
Routing
TCP/UDP
Ports
DNS
HTTP/HTTPS
Security Groups
NACLs
NAT
VPC
```

You do not need CCNP-level OSI knowledge for your current goal.

The main goal is to understand **how network traffic moves and where to troubleshoot when something fails**.
