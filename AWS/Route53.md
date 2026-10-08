# Amazon Route 53

## What is Amazon Route 53?

Amazon Route 53 is an AWS **DNS and domain management service**.

It can be used to:

* Register domains
* Manage DNS records
* Route users to applications
* Perform health checks
* Implement DNS-based routing policies

A simple example:

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
AWS Resource
```

---

## DNS

DNS (Domain Name System) converts domain names into IP addresses or other destinations.

Example:

```text
sheepeye.shop
      |
      ↓
DNS
      |
      ↓
IP / AWS Resource
```

Instead of remembering an IP address, users can access the application using a domain name.

---

## Hosted Zones

A Route 53 hosted zone contains DNS records for a domain.

There are two main types:

### Public Hosted Zone

Used for DNS records accessible through the public internet.

Example:

```text
sheepeye.shop
```

### Private Hosted Zone

Used for DNS names inside one or more VPCs.

Example:

```text
internal.sheepeye
```

Private hosted zones are useful for internal applications and services.

---

## DNS Records

Route 53 supports common DNS record types.

### A Record

Maps a domain name to an IPv4 address.

```text
example.com → 203.0.113.10
```

### AAAA Record

Maps a domain name to an IPv6 address.

### CNAME

Maps one domain name to another domain name.

```text
www.example.com
       |
       ↓
example.com
```

### MX

Used for mail servers.

### TXT

Used for text-based information such as domain verification and email-related configurations.

### NS

Specifies the authoritative name servers for a domain.

---

## Route 53 Alias Records

Route 53 supports **Alias records** for routing traffic to supported AWS resources.

Examples include:

* Application Load Balancers
* CloudFront distributions
* API Gateway
* S3 website endpoints

Example:

```text
sheepeye.shop
      |
      ↓
Route 53 Alias
      |
      ↓
CloudFront
```

Alias records are an important AWS-specific DNS feature.

---

## Route 53 and EC2

A domain can point to an EC2-hosted application.

Example:

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
EC2
 |
 ↓
Sheepeye Application
```

However, directly pointing a domain to an EC2 public IP can create problems if the IP changes.

An **Elastic IP** can provide a static public IPv4 address for an EC2 instance.

---

## Route 53 with CloudFront

A common architecture is:

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
Origin
```

The origin could be:

* S3
* Load Balancer
* EC2
* Another supported origin

This architecture can provide global content delivery and caching.

---

## Route 53 with Load Balancer

Route 53 can route a domain to an Application Load Balancer using an Alias record.

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
 ├── EC2
 ├── EC2
 └── EC2
```

This is a common highly available web architecture.

---

## Routing Policies

Route 53 provides different DNS routing policies.

### Simple Routing

Routes traffic to a single resource.

```text
User
 |
 ↓
Route 53
 |
 ↓
Resource
```

Useful for simple architectures.

---

### Weighted Routing

Distributes traffic based on assigned weights.

Example:

```text
100% Traffic
     |
 ┌───┴────┐
 ↓        ↓
90%      10%
 ↓        ↓
Version A Version B
```

Useful for testing new application versions or controlled traffic distribution.

---

### Latency-Based Routing

Routes users to the AWS Region that provides the lowest network latency.

Example:

```text
User in India
      |
      ↓
Route 53
      |
      ↓
Lowest-latency AWS Region
```

Useful for applications deployed across multiple regions.

---

### Failover Routing

Used for active-passive architectures.

```text
        Route 53
           |
     ┌─────┴─────┐
     ↓           ↓
 Primary      Secondary
   Active       Standby
```

If the primary resource becomes unhealthy, Route 53 can route traffic to the secondary resource.

---

### Geolocation Routing

Routes users based on their geographic location.

Example:

```text
Users in India
      ↓
India endpoint

Users in Europe
      ↓
Europe endpoint
```

Useful when different content or applications should be served based on user location.

---

### Geoproximity Routing

Routes traffic based on the geographic location of users and resources.

Traffic can be adjusted using a concept called **bias** to expand or shrink the geographic area served by a resource.

---

### IP-Based Routing

Routes traffic based on the source IP address of the user.

This can be useful when specific IP ranges should be directed to particular resources.

---

## Health Checks

Route 53 can perform health checks on supported endpoints.

Example:

```text
Route 53
   |
   ↓
Health Check
   |
   ↓
Application
```

If an endpoint becomes unhealthy, Route 53 can use the health information with appropriate routing configurations.

Health checks are particularly useful with failover routing.

---

## TTL

TTL stands for **Time to Live**.

It determines how long DNS resolvers can cache a DNS response.

Example:

```text
DNS Record
TTL = 300 seconds
```

A shorter TTL means changes can propagate through caches more quickly, while a longer TTL can reduce DNS query frequency.

---

## Domain Registration

Route 53 can also be used to register domains.

For example:

```text
example.com
```

DNS management and domain registration are related but separate concepts.

You can also register a domain with another registrar and use Route 53 for DNS hosting.

---

## Route 53 and HTTPS

Route 53 itself does not provide the HTTPS certificate.

For AWS applications, **AWS Certificate Manager (ACM)** can provide and manage certificates.

Example:

```text
User
 |
 ↓
HTTPS
 |
 ↓
Route 53
 |
 ↓
CloudFront / ALB
 |
 ↓
Application
```

Route 53 handles DNS while ACM handles certificates.

---

## Route 53 and Sheepeye

For Sheepeye, a possible architecture is:

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
EC2 / CloudFront
 |
 ↓
Sheepeye Application
```

For the current EC2-based setup, Route 53 can provide DNS for `sheepeye.shop`.

Later, if CloudFront or an Application Load Balancer is added, Route 53 can point the domain to that service using an Alias record.

---

## Route 53 vs DNS

DNS is the general system used to resolve domain names.

Route 53 is an AWS service that provides DNS functionality along with additional features.

```text
DNS
 ↓
General technology

Route 53
 ↓
AWS DNS + domain management + routing + health checks
```

---

## Route 53 vs CloudFront

These services have different purposes.

| Service    | Main Purpose                 |
| ---------- | ---------------------------- |
| Route 53   | DNS and traffic routing      |
| CloudFront | Content delivery and caching |

They are often used together.

```text
User
 |
 ↓
Route 53
 |
 ↓
CloudFront
 |
 ↓
Origin
```

---

## Key Points

* Route 53 is an AWS **DNS and domain management service**.
* DNS converts domain names into IP addresses or other destinations.
* Public hosted zones serve public DNS records.
* Private hosted zones provide DNS inside VPC environments.
* A records map names to IPv4 addresses.
* AAAA records map names to IPv6 addresses.
* CNAME records map one hostname to another hostname.
* Alias records can point to supported AWS resources.
* Route 53 supports multiple routing policies.
* Health checks can be used with routing configurations.
* TTL controls DNS caching duration.
* Route 53 can work with EC2, ALB, CloudFront, S3, and other AWS services.
* Route 53 handles DNS; **ACM handles HTTPS certificates**.
* Route 53 and CloudFront are commonly used together.
