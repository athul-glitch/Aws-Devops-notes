# Networking Fundamentals

Networking is the process of connecting devices so they can communicate and exchange data.

Networking is one of the foundations of cloud computing. AWS services such as EC2, VPC, RDS, Load Balancers, and Route 53 all depend on networking.

---

## 1. What is a Network?

A network connects computers, servers, phones, and other devices so they can communicate.

Example:

```text
Laptop
   |
   v
Router
   |
   v
Internet
   |
   v
Web Server
```

When you open a website, your device communicates with a server through a network.

---

## 2. Why Networking is Important in Cloud

Cloud infrastructure depends heavily on networking.

A simple cloud application may look like:

```text
User
 |
Internet
 |
Load Balancer
 |
EC2
 |
Database
```

Networking determines:

* How systems communicate
* Which systems can communicate
* How traffic reaches a server
* Which ports are allowed
* How resources access the Internet

Important AWS networking concepts include:

* VPC
* Subnets
* Route Tables
* Internet Gateway
* NAT Gateway
* Security Groups
* Network ACLs
* DNS

---

## 3. LAN

**LAN (Local Area Network)** connects devices within a small area.

Examples:

* Home network
* Office network
* College network

```text
PC ----\
Laptop --- Switch ---- Router
Server --/
```

---

## 4. WAN

**WAN (Wide Area Network)** connects networks across large geographical areas.

The Internet is the largest example of a WAN.

```text
Office A
   |
Internet
   |
Office B
```

---

## 5. Client and Server

A **client** requests a service.

A **server** provides the service.

```text
Client
  |
  | Request
  v
Server
  |
  | Response
  v
Client
```

Example:

When you open a website:

* Browser → Client
* Web Server → Server

---

## 6. IP Address

An IP address identifies a device or network interface on a network.

Example:

```text
192.168.1.10
```

Another example:

```text
10.0.1.25
```

In AWS, EC2 instances inside a VPC have private IP addresses.

---

## 7. Public and Private IP Addresses

### Private IP

Private IP addresses are used inside private networks.

Common private IPv4 ranges:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

AWS VPCs commonly use private IP ranges.

### Public IP

A public IP can be used for communication over the Internet.

Example:

```text
User
 |
Internet
 |
Public IP
 |
Server
```

---

## 8. MAC Address

A MAC address identifies a network interface at the local network level.

Example:

```text
00:1A:2B:3C:4D:5E
```

Simple difference:

```text
MAC → Local network identification
IP  → Network communication
```

---

## 9. Network Devices

### Switch

A switch connects devices within a local network.

```text
PC ----\
Laptop --- Switch
Server --/
```

### Router

A router connects different networks and forwards traffic between them.

```text
Network A
    |
  Router
    |
Network B
```

### Firewall

A firewall controls which network traffic is allowed or blocked.

AWS provides network security using services/features such as:

* Security Groups
* Network ACLs

---

## 10. Packets

Data sent across a network is divided into smaller units called packets.

```text
Large Data
    |
    v
+--------+--------+--------+
|Packet 1|Packet 2|Packet 3|
+--------+--------+--------+
    |
    v
  Network
    |
    v
Destination
```

A packet contains information such as:

* Source
* Destination
* Protocol
* Data

---

## 11. Ports

A port identifies a network service running on a device.

Common ports:

| Port | Service    |
| ---: | ---------- |
|   22 | SSH        |
|   53 | DNS        |
|   80 | HTTP       |
|  443 | HTTPS      |
|   25 | SMTP       |
| 3306 | MySQL      |
| 5432 | PostgreSQL |

Example:

```text
10.0.1.10:443
```

This means traffic is being sent to port `443`, commonly used for HTTPS.

---

## 12. Protocols

A protocol is a set of rules used for communication between systems.

Common protocols:

* TCP
* UDP
* IP
* ICMP
* ARP
* DNS
* DHCP
* HTTP
* HTTPS
* SSH

Example:

```text
Browser
   |
 HTTPS
   |
Web Server
```

---

## 13. Default Gateway

A default gateway is used when a device needs to communicate with another network.

Example:

```text
PC
 |
Router
 |
Internet
 |
Website
```

The router acts as the default gateway.

In cloud networking, gateways are also used to connect networks and control traffic flow.

---

## 14. Basic Network Communication

When a user opens a website, several networking concepts work together:

```text
User
 |
DNS
 |
IP Address
 |
Routing
 |
Server
 |
Port
 |
Application
 |
Response
 |
User
```

Understanding this flow is important for cloud troubleshooting.

---

## 15. Networking and AWS

AWS networking uses the same fundamental networking concepts.

A basic AWS network can look like:

```text
Internet
    |
    v
Internet Gateway
    |
    v
VPC
    |
    v
Subnet
    |
    v
EC2
```

For example:

* **VPC** → Private network in AWS
* **Subnet** → Smaller network inside a VPC
* **Route Table** → Controls where traffic goes
* **Internet Gateway** → Provides Internet connectivity
* **Security Group** → Controls traffic to/from resources

---

## 16. Simple Cloud Example

Consider an application running on EC2.

```text
User
 |
Internet
 |
Internet Gateway
 |
Public Subnet
 |
EC2
```

If the EC2 application also needs to communicate with a database:

```text
User
 |
Internet
 |
Load Balancer
 |
EC2
 |
Private Database
```

The database can remain private while the application is accessible to users.

This is a common cloud networking design.

---

## 17. Basic Networking Troubleshooting

When a server cannot be reached, check the problem step by step.

```text
DNS
 ↓
IP Address
 ↓
Route
 ↓
Firewall / Security Group
 ↓
Port
 ↓
Service
 ↓
Application
```

Useful Linux commands:

```bash
ip addr
```

Shows network interfaces and IP addresses.

```bash
ip route
```

Shows the routing table.

```bash
ping 8.8.8.8
```

Tests basic network connectivity.

```bash
curl https://example.com
```

Tests HTTP/HTTPS connectivity.

```bash
ss -tuln
```

Shows listening network ports.

```bash
nslookup example.com
```

Checks DNS resolution.

```bash
dig example.com
```

Provides detailed DNS information.

---

## Key Points

* A network allows devices to communicate.
* IP addresses are used for network communication.
* MAC addresses identify network interfaces locally.
* Routers connect different networks.
* Switches connect devices within a local network.
* Ports identify network services.
* Protocols define communication rules.
* Packets carry data across networks.
* A default gateway provides a path to other networks.
* AWS networking is built on these basic networking concepts.
* Strong networking fundamentals are essential for cloud infrastructure.
