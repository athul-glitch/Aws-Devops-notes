# DNS

## Introduction

**DNS (Domain Name System)** translates human-readable domain names into IP addresses.

For example:

```text
sheepeye.shop
      ↓
IP Address
```

Instead of remembering an IP address such as:

```text
13.234.100.50
```

users can access:

```text
https://sheepeye.shop
```

DNS is one of the most important networking concepts for cloud engineers.

---

# 1. Why DNS is Needed

Computers communicate using IP addresses, but humans prefer domain names.

Without DNS:

```text
https://13.234.100.50
```

With DNS:

```text
https://sheepeye.shop
```

DNS connects the domain name to the appropriate network destination.

---

# 2. How DNS Works

A simplified DNS lookup looks like this:

```text
User
 |
 | "What is the IP of sheepeye.shop?"
 v
DNS Resolver
 |
 v
DNS Server
 |
 v
IP Address
 |
 v
Web Server
```

The browser can then connect to the returned IP address.

---

# 3. Domain Name

A domain name is a human-readable name used to identify a website or service.

Examples:

```text
google.com
amazon.com
sheepeye.shop
```

A domain can have subdomains:

```text
example.com
www.example.com
api.example.com
app.example.com
```

For example:

```text
api.sheepeye.shop
```

could be used for an application's API.

---

# 4. DNS Resolver

A **DNS resolver** receives DNS queries from clients and finds the required DNS information.

For example:

```text
Your Laptop
     |
     | DNS Query
     v
DNS Resolver
     |
     v
DNS Servers
```

The resolver may use cached information to answer quickly.

---

# 5. DNS Cache

DNS responses can be cached.

For example:

```text
User → DNS Resolver
          |
          | Cached result
          v
       IP Address
```

Caching reduces DNS lookup time and reduces repeated DNS queries.

The amount of time a DNS record can be cached is controlled by **TTL**.

---

# 6. TTL

**TTL (Time To Live)** specifies how long a DNS record can be cached.

Example:

```text
TTL = 300 seconds
```

This means the record can normally be cached for about:

```text
5 minutes
```

A lower TTL can allow DNS changes to be reflected sooner, while a higher TTL can reduce DNS query traffic.

---

# 7. DNS Records

DNS uses different types of records for different purposes.

Important records include:

* A
* AAAA
* CNAME
* MX
* NS
* TXT
* Alias

---

# 8. A Record

An **A record** maps a domain name to an IPv4 address.

Example:

```text
example.com → 203.0.113.10
```

In simple form:

```text
A Record
   |
   v
Domain → IPv4
```

---

# 9. AAAA Record

An **AAAA record** maps a domain name to an IPv6 address.

Example:

```text
example.com → IPv6 address
```

Remember:

```text
A     → IPv4
AAAA  → IPv6
```

---

# 10. CNAME Record

A **CNAME (Canonical Name)** record maps one domain name to another domain name.

Example:

```text
www.example.com
       |
       v
example.com
```

CNAME is useful when you want one domain name to point to another hostname.

---

# 11. MX Record

**MX (Mail Exchange)** records specify the mail servers responsible for receiving email for a domain.

Example:

```text
example.com
     |
     v
MX Record
     |
     v
Mail Server
```

MX records are used for email services.

---

# 12. NS Record

**NS (Name Server)** records identify the authoritative DNS name servers for a domain.

Example:

```text
example.com
     |
     v
NS Records
     |
     v
Authoritative DNS Servers
```

---

# 13. TXT Record

A **TXT record** stores text information associated with a domain.

TXT records are commonly used for:

* Domain verification
* SPF
* DKIM-related configuration
* Email security
* Service verification

---

# 14. DNS Lookup Example

Suppose you enter:

```text
https://sheepeye.shop
```

The simplified process is:

```text
Browser
   |
   | DNS query
   v
DNS Resolver
   |
   v
DNS
   |
   | IP address
   v
Browser
   |
   | HTTPS request
   v
Web Server
```

DNS happens before the browser can connect to the destination using the hostname.

---

# 15. DNS and AWS

AWS provides a DNS service called **Amazon Route 53**.

Route 53 can be used for:

* Domain DNS management
* DNS routing
* Health checks
* Domain registration
* Routing traffic to AWS resources

---

# 16. Route 53 Example

Suppose your domain is:

```text
sheepeye.shop
```

You can use Route 53 to manage DNS records.

Example:

```text
sheepeye.shop
       |
       v
Route 53
       |
       v
AWS Resource
```

The AWS resource could be:

* EC2
* Load Balancer
* CloudFront
* API Gateway
* Another supported endpoint

---

# 17. Route 53 Hosted Zone

A **hosted zone** is a container for DNS records for a domain.

For example:

```text
Hosted Zone
   |
   ├── A Record
   ├── CNAME
   ├── MX
   ├── TXT
   └── Other Records
```

A public hosted zone is used for DNS information that should be available on the internet.

A private hosted zone can be used with VPCs for internal DNS.

---

# 18. Public vs Private DNS

### Public DNS

Used for internet-accessible names.

Example:

```text
www.example.com
```

### Private DNS

Used inside private networks such as an AWS VPC.

Example:

```text
database.internal
```

Private DNS can allow internal resources to communicate using names instead of private IP addresses.

---

# 19. DNS in an AWS VPC

AWS VPCs provide DNS functionality for resources inside the VPC.

For example:

```text
EC2
 |
 | DNS lookup
 v
VPC DNS
 |
 v
Private Resource
```

This allows applications to resolve internal hostnames.

---

# 20. DNS and Load Balancers

DNS is commonly used with load balancers.

Example:

```text
User
 |
 | app.example.com
 v
Route 53
 |
 v
Application Load Balancer
 |
 +---- EC2
 |
 +---- EC2
 |
 +---- EC2
```

Users don't need to know the individual EC2 IP addresses.

---

# 21. DNS and CloudFront

DNS is also commonly used with CloudFront.

Example:

```text
User
 |
 | www.example.com
 v
Route 53
 |
 v
CloudFront
 |
 v
Origin
```

The origin could be:

* S3
* Load Balancer
* EC2-based application
* Another supported origin

This is a common AWS architecture.

---

# 22. Alias Record in Route 53

AWS Route 53 supports **Alias records**.

Alias records can point a domain name to supported AWS resources such as:

* CloudFront distributions
* Load Balancers
* API Gateway
* S3 website endpoints in supported configurations

Example:

```text
www.example.com
       |
       v
Route 53 Alias
       |
       v
CloudFront
```

### Alias vs CNAME

| Feature                         | Alias | CNAME   |
| ------------------------------- | ----- | ------- |
| AWS Route 53                    | Yes   | Yes     |
| Points to hostname              | Yes   | Yes     |
| Can point to some AWS resources | Yes   | Limited |
| Used at zone apex               | Yes   | No      |

The **zone apex** means the root domain:

```text
example.com
```

rather than:

```text
www.example.com
```

---

# 23. DNS Troubleshooting

DNS problems are common in cloud environments.

If a website cannot be accessed, check:

1. Is the domain registered?
2. Are the correct name servers configured?
3. Does the DNS record exist?
4. Is the record pointing to the correct destination?
5. Is the DNS record cached?
6. Is the destination reachable?
7. Is the Security Group allowing the required traffic?
8. Is HTTPS configured correctly?

---

# 24. Useful Linux Commands

### nslookup

```bash
nslookup example.com
```

Used to query DNS information.

### dig

```bash
dig example.com
```

Provides detailed DNS information.

### Query a specific record

```bash
dig example.com A
```

### Check CNAME

```bash
dig www.example.com CNAME
```

### Check MX

```bash
dig example.com MX
```

### Ping a hostname

```bash
ping example.com
```

Note: `ping` also tests network reachability and is not purely a DNS troubleshooting command.

---

# 25. Simple Cloud Example

Suppose you have:

```text
sheepeye.shop
       |
       v
Route 53
       |
       v
CloudFront
       |
       v
Application
```

The user enters:

```text
https://sheepeye.shop
```

DNS helps the client find the appropriate destination.

After DNS resolution, the browser establishes an HTTPS connection to the destination.

---

# 26. Important DNS Terms

| Term        | Meaning                                     |
| ----------- | ------------------------------------------- |
| DNS         | Domain Name System                          |
| Resolver    | Finds DNS information for clients           |
| Record      | DNS entry containing information            |
| TTL         | How long a record can be cached             |
| A           | IPv4 address                                |
| AAAA        | IPv6 address                                |
| CNAME       | Alias to another hostname                   |
| MX          | Mail server information                     |
| NS          | Authoritative name servers                  |
| TXT         | Text/verification information               |
| Route 53    | AWS DNS service                             |
| Hosted Zone | Container for DNS records                   |
| Alias       | Route 53 record for supported AWS resources |

---

# Key Points

* DNS translates domain names into network destinations.
* DNS makes websites easier for humans to access.
* A record → IPv4.
* AAAA record → IPv6.
* CNAME → another hostname.
* MX → mail servers.
* NS → name servers.
* TXT → text and verification information.
* TTL controls DNS caching duration.
* Route 53 is AWS's DNS service.
* DNS is heavily used with EC2, Load Balancers, CloudFront, and other AWS services.
* DNS troubleshooting is an important cloud support skill.

---

## Cloud Engineer Focus

For AWS roles, focus mainly on:

**DNS → A/AAAA/CNAME → TTL → Route 53 → Hosted Zones → Alias → DNS troubleshooting**

You do not need to study advanced DNS server administration at this stage.
