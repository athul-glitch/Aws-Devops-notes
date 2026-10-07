# DHCP

## Introduction

**DHCP (Dynamic Host Configuration Protocol)** automatically provides network configuration to devices.

Instead of manually configuring an IP address, a device can request network information from a DHCP server.

DHCP can provide:

* IP address
* Subnet mask
* Default gateway
* DNS server
* Lease duration

---

# 1. Why DHCP is Needed

Without DHCP, every device would need to be configured manually.

Example:

```text
Device
  |
  | DHCP Request
  v
DHCP Server
  |
  | IP configuration
  v
Device
```

DHCP makes network configuration automatic.

---

# 2. DHCP Process

DHCP commonly uses a four-step process called **DORA**.

```text
Client                  DHCP Server

  |---- DHCP Discover ---->|
  |<----- DHCP Offer ------|
  |---- DHCP Request ----->|
  |<----- DHCP ACK --------|
```

### D — Discover

The client searches for a DHCP server.

### O — Offer

The DHCP server offers an IP address and other network information.

### R — Request

The client requests the offered configuration.

### A — Acknowledgement

The server confirms the configuration.

---

# 3. Example

A device joins a network.

The DHCP server may provide:

```text
IP Address:      192.168.1.20
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
DNS Server:      8.8.8.8
```

The device can then communicate with other devices and access the internet.

---

# 4. DHCP Ports

DHCP normally uses **UDP**.

| Purpose     |   Port |
| ----------- | -----: |
| DHCP Server | UDP 67 |
| DHCP Client | UDP 68 |

Remember:

```text
DHCP → UDP 67/68
```

---

# 5. DHCP Lease

DHCP usually gives an IP address for a limited period called a **lease**.

Example:

```text
Device
   |
   | Request IP
   v
DHCP Server
   |
   | Lease: 8 hours
   v
Device
```

The client can renew the lease before it expires.

---

# 6. DHCP and Private Networks

DHCP is commonly used in local networks.

Example:

```text
Laptop
   |
   v
Wi-Fi Router
   |
   v
DHCP Server
   |
   v
Private IP
```

The router or another DHCP server can automatically assign private IP addresses.

---

# 7. DHCP and AWS

AWS networking works differently from a typical home or office network.

When an EC2 instance is launched into an AWS VPC, AWS automatically provides network configuration such as a private IPv4 address.

AWS VPC also provides DHCP-related configuration through **DHCP option sets**.

DHCP option sets can specify settings such as:

* Domain name
* DNS servers
* NTP servers
* NetBIOS settings

---

# 8. DHCP Option Sets in AWS

An AWS VPC can have a DHCP option set associated with it.

Example:

```text
VPC
 |
 | DHCP Options
 |
 +---- Domain Name
 |
 +---- DNS Servers
 |
 +---- NTP Servers
```

This allows administrators to customize certain network configuration parameters for resources in the VPC.

---

# 9. DHCP vs DNS

These two are different.

| DHCP                           | DNS                     |
| ------------------------------ | ----------------------- |
| Provides network configuration | Resolves names          |
| Can provide IP address         | Maps names to addresses |
| Uses UDP 67/68                 | Commonly UDP/TCP 53     |
| Helps configure devices        | Helps find services     |

Example:

```text
DHCP
  ↓
"What IP configuration should I use?"

DNS
  ↓
"What IP address belongs to example.com?"
```

---

# 10. Basic Troubleshooting

If a device does not receive an IP address, possible causes include:

* DHCP server unavailable
* Network connectivity problem
* Incorrect network configuration
* DHCP request being blocked
* Address pool exhausted

On Linux, check the assigned addresses with:

```bash
ip addr
```

Check the routing table with:

```bash
ip route
```

---

# Key Points

* DHCP automatically provides network configuration.
* DHCP commonly uses UDP.
* Server port → **67**
* Client port → **68**
* DORA = Discover, Offer, Request, Acknowledgement.
* DHCP uses leases.
* AWS VPCs support DHCP-related configuration through DHCP option sets.
* DHCP and DNS have different purposes.

---

## Cloud Engineer Focus

For your AWS career, you mainly need to remember:

**DHCP → DORA → UDP 67/68 → IP configuration → AWS DHCP Option Sets**

You don't need deep DHCP server administration at this stage.
