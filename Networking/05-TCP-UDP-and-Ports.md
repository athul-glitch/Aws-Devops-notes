# TCP, UDP and Ports

## Introduction

TCP and UDP are transport-layer protocols used to deliver data between applications over a network.

Ports identify which application or service should receive the network traffic.

Understanding TCP, UDP, and ports is important for AWS services such as:

* EC2
* Security Groups
* Network ACLs
* Load Balancers
* RDS
* DNS
* SSH
* HTTP/HTTPS

---

## 1. What is TCP?

**TCP (Transmission Control Protocol)** is a connection-oriented and reliable transport protocol.

TCP provides:

* Reliable delivery
* Ordered data
* Error checking
* Retransmission of lost data
* Flow control
* Congestion control

TCP is used when reliable communication is important.

### Common TCP examples

* SSH — Port 22
* HTTP — Port 80
* HTTPS — Port 443
* SMTP — Port 25
* MySQL — Port 3306
* PostgreSQL — Port 5432
* RDP — Port 3389

---

## 2. TCP Three-Way Handshake

Before sending data, TCP normally establishes a connection using a three-way handshake.

```text
Client                 Server

   SYN  ------------->

        <------------- SYN-ACK

   ACK  ------------->

       Connection Established
```

### Steps

1. **SYN** — Client requests a connection.
2. **SYN-ACK** — Server accepts and responds.
3. **ACK** — Client confirms.

After this, data can be exchanged.

---

## 3. What is UDP?

**UDP (User Datagram Protocol)** is a connectionless transport protocol.

UDP does not guarantee:

* Delivery
* Ordering
* Retransmission

However, UDP has lower overhead and can be faster than TCP for applications where speed is more important than guaranteed delivery.

### Common UDP examples

* DNS — Port 53
* DHCP — Ports 67 and 68
* Some video/audio streaming
* Online gaming
* Real-time communication

---

## 4. TCP vs UDP

| Feature        | TCP                 | UDP            |
| -------------- | ------------------- | -------------- |
| Connection     | Connection-oriented | Connectionless |
| Reliability    | Reliable            | No guarantee   |
| Ordering       | Maintains order     | No guarantee   |
| Retransmission | Yes                 | No             |
| Speed/overhead | More overhead       | Lower overhead |
| Handshake      | Yes                 | No             |
| Example        | HTTPS, SSH          | DNS, DHCP      |

### Easy way to remember

**TCP = Reliable**

**UDP = Lightweight and fast**

---

# 5. What is a Port?

A **port** identifies a specific service or application running on a device.

An IP address identifies the **host**.

A port identifies the **service** on that host.

Example:

```text
192.168.1.10:443
```

Here:

* `192.168.1.10` = IP address
* `443` = Port
* TCP = Transport protocol
* HTTPS = Service

---

## 6. Port Ranges

Ports range from:

```text
0 - 65535
```

They are commonly divided into:

| Range       | Purpose                 |
| ----------- | ----------------------- |
| 0–1023      | Well-known ports        |
| 1024–49151  | Registered ports        |
| 49152–65535 | Dynamic/Ephemeral ports |

---

# 7. Important Ports

|  Port | Protocol/Service | Transport |
| ----: | ---------------- | --------- |
| 20/21 | FTP              | TCP       |
|    22 | SSH              | TCP       |
|    23 | Telnet           | TCP       |
|    25 | SMTP             | TCP       |
|    53 | DNS              | TCP/UDP   |
| 67/68 | DHCP             | UDP       |
|    80 | HTTP             | TCP       |
|   110 | POP3             | TCP       |
|   143 | IMAP             | TCP       |
|   443 | HTTPS            | TCP       |
|  3306 | MySQL            | TCP       |
|  3389 | RDP              | TCP       |
|  5432 | PostgreSQL       | TCP       |

> Telnet is generally considered insecure because it does not provide encrypted communication. SSH is preferred for secure remote administration.

---

# 8. Ports in AWS

Ports are extremely important when working with AWS networking.

For example, suppose an application is running on an EC2 instance.

```text
Internet
   |
   | HTTPS :443
   |
   v
EC2 Instance
```

The EC2 Security Group must allow the required traffic.

For example:

```text
Inbound Rule

Type: HTTPS
Protocol: TCP
Port: 443
Source: 0.0.0.0/0
```

This allows HTTPS traffic to reach the instance.

---

# 9. SSH Example

To connect to a Linux EC2 instance using SSH:

```bash
ssh -i key.pem ec2-user@PUBLIC-IP
```

SSH normally uses:

```text
TCP Port 22
```

The Security Group needs an inbound rule allowing TCP port 22.

For better security, SSH should normally be restricted to your own IP instead of:

```text
0.0.0.0/0
```

---

# 10. Database Port Example

Suppose:

```text
EC2 Application
      |
      | TCP 5432
      |
      v
PostgreSQL
```

PostgreSQL normally uses:

```text
TCP Port 5432
```

A good AWS design is to allow port 5432 only from the application's Security Group rather than exposing the database to the entire internet.

Example:

```text
Internet
   |
   v
EC2 / Application
   |
   | TCP 5432
   |
   v
RDS PostgreSQL
```

This is much safer than allowing:

```text
5432 from 0.0.0.0/0
```

---

# 11. Listening Ports

A server application usually listens on a particular port.

For example:

```text
Web Server → 80/443
SSH → 22
PostgreSQL → 5432
MySQL → 3306
```

You can check listening ports on Linux using:

```bash
ss -tuln
```

For more information about processes:

```bash
ss -lntp
```

---

# 12. Testing a Port

You can use `curl` to test HTTP/HTTPS services:

```bash
curl http://example.com
```

or:

```bash
curl https://example.com
```

You can also use tools such as `nc` (netcat) to test whether a TCP port is reachable:

```bash
nc -zv <IP> 443
```

Example:

```bash
nc -zv 10.0.1.10 443
```

---

# 13. Connection Refused vs Timeout

These are common troubleshooting situations.

### Connection refused

The host may be reachable, but the service is not accepting connections on that port.

Possible reasons:

* Application is not running
* Service is listening on another port
* Local firewall is rejecting the connection

### Connection timeout

The connection does not receive a response within the expected time.

Possible reasons:

* Security Group blocking traffic
* Network ACL blocking traffic
* Routing problem
* Firewall
* Service or host unavailable

These are general troubleshooting clues, not absolute rules.

---

# 14. Simple AWS Example

Imagine you deploy a web application on EC2.

```text
User
 |
 | HTTPS :443
 v
EC2
 |
 | PostgreSQL :5432
 v
RDS PostgreSQL
```

Required traffic might be:

```text
Internet → EC2
TCP 443

EC2 → RDS
TCP 5432
```

The EC2 Security Group can allow HTTPS from the internet, while the RDS Security Group can allow PostgreSQL traffic only from the EC2 application's Security Group.

---

# 15. Linux Commands

### Show IP address

```bash
ip addr
```

### Show listening ports

```bash
ss -tuln
```

### Show listening TCP ports with processes

```bash
ss -lntp
```

### Test HTTP/HTTPS

```bash
curl https://example.com
```

### Test DNS

```bash
nslookup example.com
```

---

# Key Points

* TCP is connection-oriented and reliable.
* UDP is connectionless and has lower overhead.
* A port identifies a service or application.
* Ports range from 0 to 65535.
* HTTP uses port 80.
* HTTPS uses port 443.
* SSH uses port 22.
* DNS commonly uses port 53.
* PostgreSQL uses port 5432.
* MySQL uses port 3306.
* AWS Security Groups control allowed network traffic to and from resources.
* Never expose database ports to the internet unless there is a specific requirement.
* Understanding ports is essential for AWS troubleshooting.

---

## Cloud Engineer Focus

For your AWS/cloud career, focus mainly on:

**TCP vs UDP → Ports → Common ports → Security Groups → Connectivity troubleshooting**

You do not need to study TCP/UDP at CCNP-level depth.
