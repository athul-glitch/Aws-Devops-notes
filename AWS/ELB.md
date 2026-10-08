# Elastic Load Balancing

## What is Elastic Load Balancing?

Elastic Load Balancing (ELB) distributes incoming traffic across multiple targets.

Targets can include:

* EC2 instances
* Containers
* IP addresses
* Lambda functions

Basic architecture:

```text
Users
  |
  ↓
Load Balancer
  |
 ┌┴─────────┐
 ↓          ↓
EC2        EC2
```

The load balancer helps distribute traffic instead of sending all requests to a single server.

---

## Why Use a Load Balancer?

A load balancer can provide:

* High availability
* Traffic distribution
* Health checks
* Scalability
* Fault tolerance
* SSL/TLS termination

Without a load balancer:

```text
Users
  |
  ↓
Single EC2
```

If that EC2 instance fails, the application becomes unavailable.

With a load balancer:

```text
Users
  |
  ↓
Load Balancer
  |
 ┌┴─────────┐
 ↓          ↓
EC2        EC2
```

If one instance fails, traffic can be sent to the healthy instance.

---

## Types of Elastic Load Balancers

AWS provides three main types:

### Application Load Balancer (ALB)

Used mainly for **HTTP/HTTPS** applications.

Features include:

* Path-based routing
* Host-based routing
* HTTP/HTTPS
* WebSocket support
* Integration with ECS and Lambda

Example:

```text
example.com/api
      |
      ↓
     ALB
      |
      ↓
API Servers
```

---

### Network Load Balancer (NLB)

Used for high-performance **TCP, UDP, and TLS** traffic.

NLB is suitable when:

* Very high performance is required
* Low latency is important
* TCP/UDP traffic must be supported
* Static IP requirements exist

Example:

```text
Client
  |
  ↓
NLB
  |
  ├── Server 1
  └── Server 2
```

---

### Gateway Load Balancer (GWLB)

Used to deploy and scale network security appliances.

Example:

```text
Traffic
   |
   ↓
GWLB
   |
   ↓
Security Appliance
   |
   ↓
Application
```

It is commonly associated with network virtual appliances such as firewalls and inspection systems.

---

## ALB vs NLB vs GWLB

| Load Balancer | Main Use                             |
| ------------- | ------------------------------------ |
| ALB           | HTTP/HTTPS applications              |
| NLB           | TCP/UDP/TLS high-performance traffic |
| GWLB          | Network security appliances          |

For typical web applications, **ALB** is usually the relevant choice.

---

## Target Groups

A target group contains the targets that receive traffic from a load balancer.

Example:

```text
ALB
 |
 ↓
Target Group
 |
 ├── EC2
 ├── EC2
 └── EC2
```

Targets can be registered with a target group.

---

## Health Checks

Load balancers use health checks to determine whether targets are available.

Example:

```text
ALB
 |
 ↓
Health Check
 |
 ├── EC2-1 → Healthy
 ├── EC2-2 → Healthy
 └── EC2-3 → Unhealthy
```

The load balancer can stop sending traffic to an unhealthy target.

A common HTTP health check could use:

```text
GET /health
```

If the application returns a successful response, the target can be considered healthy.

---

## Listeners

A listener checks for incoming connection requests on a specified protocol and port.

Examples:

```text
HTTP  → Port 80
HTTPS → Port 443
```

Example:

```text
Internet
   |
   ↓
ALB
 |
 ├── Listener :80
 └── Listener :443
```

The listener uses rules to determine where traffic should go.

---

## Listener Rules

ALB supports routing rules based on request information.

### Path-Based Routing

Example:

```text
example.com/api/*
        |
        ↓
API Target Group

example.com/images/*
        |
        ↓
Image Target Group
```

Different URL paths can be sent to different target groups.

---

### Host-Based Routing

Different domain names can route to different target groups.

Example:

```text
api.example.com
       |
       ↓
API Target Group

app.example.com
       |
       ↓
Web Target Group
```

---

## ALB Architecture

A common architecture is:

```text
Internet
   |
   ↓
Route 53
   |
   ↓
ALB
   |
   ↓
Target Group
   |
 ┌─┴────────┐
 ↓          ↓
EC2        EC2
```

The ALB should normally be deployed across multiple Availability Zones for high availability.

---

## Public and Private Load Balancers

A load balancer can be:

### Internet-facing

Accessible from the internet.

Example:

```text
Internet
   |
   ↓
Internet-facing ALB
   |
   ↓
Private EC2 Instances
```

This is a common web application architecture.

### Internal

Accessible only from inside a VPC or connected networks.

Example:

```text
Application
     |
     ↓
Internal ALB
     |
     ↓
Backend Services
```

Internal load balancers are useful for internal applications and microservices.

---

## SSL/TLS Termination

An ALB can terminate HTTPS connections.

Example:

```text
User
 |
 | HTTPS :443
 ↓
ALB
 |
 | HTTP :80
 ↓
EC2
```

The TLS certificate can be managed using **AWS Certificate Manager (ACM)**.

This can reduce the need to configure certificates individually on every backend server.

---

## Load Balancer Security Groups

An internet-facing ALB can have a Security Group allowing public HTTP/HTTPS traffic.

The EC2 instances can use another Security Group that allows traffic **only from the ALB Security Group**.

Example:

```text
Internet
   |
   ↓
ALB Security Group
   |
   ↓
EC2 Security Group
```

This is more secure than allowing the entire internet to directly access the EC2 instances.

---

## Load Balancer and Auto Scaling

ALB is commonly combined with Auto Scaling.

```text
Users
  |
  ↓
ALB
  |
  ↓
Target Group
  |
 ┌┴─────────────┐
 ↓              ↓
EC2            EC2
 ↑              ↑
 └── Auto Scaling ──┘
```

When demand increases, Auto Scaling can launch additional instances.

The new instances can automatically be registered with the target group.

---

## ALB and CloudFront

CloudFront can be placed in front of an ALB.

```text
User
 |
 ↓
CloudFront
 |
 ↓
ALB
 |
 ↓
EC2
```

CloudFront provides CDN and caching capabilities, while ALB distributes application traffic.

---

## ALB and Route 53

Route 53 can route a domain to an ALB using an Alias record.

```text
User
 |
 ↓
example.com
 |
 ↓
Route 53
 |
 ↓
ALB
 |
 ↓
EC2
```

---

## Load Balancer and Sheepeye

A future highly available Sheepeye architecture could look like:

```text
User
 |
 ↓
sheepeye.shop
 |
 ↓
Route 53
 |
 ↓
CloudFront
 |
 ↓
ALB
 |
 ↓
Target Group
 |
 ├── EC2
 └── EC2
```

Auto Scaling could later add or remove EC2 instances based on demand.

However, you **do not need to add an ALB immediately** to Sheepeye.

Since you are keeping AWS costs low, your current single-EC2 architecture is fine for learning. ALB becomes useful when you want to demonstrate high availability and scaling.

---

## Load Balancer vs Reverse Proxy

A load balancer can also act as a reverse proxy.

Instead of users connecting directly to backend servers:

```text
User → EC2
```

they connect to the load balancer:

```text
User → ALB → EC2
```

The backend infrastructure can remain hidden from direct public access.

---

## Key Points

* ELB distributes incoming traffic across multiple targets.
* **ALB** is mainly for HTTP/HTTPS applications.
* **NLB** is designed for high-performance TCP/UDP/TLS traffic.
* **GWLB** is used for network security appliances.
* Target groups contain the resources receiving traffic.
* Health checks identify healthy and unhealthy targets.
* Listeners accept traffic on specific ports and protocols.
* ALB supports host-based and path-based routing.
* ALB can terminate HTTPS using ACM certificates.
* Load balancers can be internet-facing or internal.
* ALB is commonly combined with Auto Scaling.
* CloudFront can be placed in front of an ALB.
* Route 53 can route a domain to an ALB using an Alias record.
* For a production-style architecture, ALB should span multiple Availability Zones.
* **ALB = application-level HTTP/HTTPS traffic.**
* **NLB = high-performance network-level traffic.**
* **GWLB = network security appliances.**
