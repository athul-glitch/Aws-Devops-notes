# AWS Certificate Manager (ACM)

## What is AWS Certificate Manager?

AWS Certificate Manager (**ACM**) is a managed AWS service used to **provision, manage, and renew SSL/TLS certificates**.

SSL/TLS certificates allow applications to use **HTTPS**.

```text
User
  |
  | HTTPS
  ↓
AWS Service
  |
  ↓
Application
```

ACM removes much of the manual work involved in obtaining and renewing certificates.

---

## Why Use ACM?

Without a certificate, a website may use:

```text
http://example.com
```

With a valid SSL/TLS certificate:

```text
https://example.com
```

HTTPS provides encrypted communication between the client and the application.

ACM is useful because it can:

* Provision certificates
* Validate domain ownership
* Manage certificates
* Automatically renew eligible certificates
* Integrate with AWS services

---

# SSL/TLS

### SSL

SSL is the older technology historically used for secure communication.

### TLS

TLS is the modern protocol used to secure HTTPS connections.

In AWS discussions, you will commonly hear:

```text
SSL/TLS certificate
```

The certificate establishes the identity of the domain and enables encrypted communication.

---

# HTTP vs HTTPS

### HTTP

```text
Browser
   |
   | HTTP
   ↓
Server
```

Traffic is not protected by TLS encryption.

### HTTPS

```text
Browser
   |
   | HTTPS
   ↓
Server
```

TLS protects the communication.

---

# ACM Certificate

An ACM certificate can be issued for a domain such as:

```text
example.com
```

It can also cover subdomains depending on the certificate configuration.

Example:

```text
example.com
www.example.com
api.example.com
```

---

# Domain Validation

Before ACM issues a public certificate, AWS needs to verify that you control the domain.

ACM supports validation methods such as:

* DNS validation
* Email validation

### DNS Validation

DNS validation is generally preferred because it can support automated renewal.

Conceptually:

```text
Your Domain
     |
     ↓
DNS Record
     |
     ↓
ACM verifies ownership
     |
     ↓
Certificate issued
```

---

# DNS Validation with Route 53

When the domain uses Route 53, ACM can make DNS validation easier.

Example:

```text
Route 53
   |
   ↓
Validation Record
   |
   ↓
ACM
   |
   ↓
Certificate
```

For a domain managed through Route 53, the required validation record can be created in the hosted zone.

---

# Email Validation

ACM can also use email validation.

AWS sends validation instructions to approved administrative email addresses associated with the domain.

The domain owner must complete the validation process.

DNS validation is often more convenient for automated certificate management.

---

# Certificate Renewal

One of ACM's major advantages is managed certificate renewal.

For eligible ACM-issued certificates that remain in use with integrated AWS services, AWS can automatically renew them.

Conceptually:

```text
Certificate
     |
     ↓
Approaching expiration
     |
     ↓
ACM renewal process
     |
     ↓
New certificate
```

This reduces the risk of certificates unexpectedly expiring.

---

# ACM and CloudFront

ACM is commonly used with **CloudFront** to provide HTTPS.

Important point:

> A certificate used with Amazon CloudFront must be in the **US East (N. Virginia) — `us-east-1` Region**.

Example:

```text
User
  |
  | HTTPS
  ↓
CloudFront
  |
  ↓
Origin
```

The ACM certificate is associated with the CloudFront distribution.

---

# ACM and Application Load Balancer

ACM can also provide certificates for an **Application Load Balancer (ALB)**.

Example:

```text
User
  |
  | HTTPS
  ↓
ALB
  |
  ↓
EC2
```

The certificate can be attached to an HTTPS listener on the ALB.

---

# TLS Termination

A common architecture is to terminate TLS at the load balancer.

```text
User
  |
  | HTTPS
  ↓
ALB
  |
  | HTTP or HTTPS
  ↓
EC2
```

The ALB handles the TLS connection from the client.

This is called **TLS termination**.

The backend connection can then be configured separately depending on security requirements.

---

# ACM with CloudFront and ALB

A common architecture is:

```text
User
  |
  | HTTPS
  ↓
CloudFront
  |
  | HTTPS
  ↓
ALB
  |
  ↓
EC2
```

Certificates can be used at the appropriate HTTPS endpoints.

Remember:

```text
CloudFront certificate → us-east-1
ALB certificate        → ALB's Region
```

---

# ACM and Route 53

Route 53 and ACM are often used together.

Example:

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
Application
```

ACM provides the certificate:

```text
ACM
 ↓
HTTPS certificate
 ↓
CloudFront
```

Route 53 handles DNS.

---

# ACM Public Certificates

ACM can issue public certificates for publicly trusted domain names.

Example:

```text
sheepeye.shop
```

The certificate allows browsers to establish a trusted HTTPS connection.

---

# ACM Private CA

AWS Certificate Manager Private Certificate Authority (**ACM Private CA**) is used for private certificates.

Private certificates are useful for internal applications and private networks.

Example:

```text
Internal Application
       |
       ↓
Private Certificate
       |
       ↓
Internal HTTPS
```

This is different from a publicly trusted certificate used by websites on the public Internet.

---

# Public vs Private Certificates

| Feature       | Public Certificate       | Private Certificate       |
| ------------- | ------------------------ | ------------------------- |
| Main use      | Public websites/services | Internal/private systems  |
| Browser trust | Public trust chain       | Private CA trust required |
| Example       | `example.com`            | Internal service          |
| Typical use   | Public HTTPS             | Internal TLS              |

---

# ACM Certificate Import

ACM can also support importing certificates obtained from external certificate authorities.

However, imported certificates are managed differently from certificates issued directly by ACM.

For most AWS-managed public HTTPS use cases, ACM-issued certificates are convenient because of AWS integration and managed renewal.

---

# ACM and IAM

ACM manages certificates, while IAM manages **who can perform actions on AWS resources**.

Example:

```text
IAM
 ↓
Who can manage certificate
 ↓
ACM
 ↓
Certificate
```

IAM policies can control permissions related to ACM operations.

---

# ACM and Security Groups

ACM does not replace security groups.

Example:

```text
Internet
   |
   | TCP 443
   ↓
Security Group
   |
   ↓
ALB
   |
   ↓
Application
```

ACM provides the certificate.

The security group controls network access.

---

# ACM and Port 443

HTTPS normally uses:

```text
TCP 443
```

Example:

```text
Internet
   |
   | TCP 443
   ↓
ALB / CloudFront
```

For an ALB, the security group must allow the required HTTPS traffic.

---

# ACM and Port 80

HTTP normally uses:

```text
TCP 80
```

A common website configuration is:

```text
HTTP :80
   |
   ↓
Redirect
   |
   ↓
HTTPS :443
```

This can ensure users access the application securely.

---

# ACM and Sheepeye

Your Sheepeye project uses:

```text
sheepeye.shop
```

A future HTTPS architecture could be:

```text
User
  |
  | HTTPS
  ↓
Route 53
  |
  ↓
CloudFront
  |
  ↓
EC2 / Application
```

ACM would provide the TLS certificate.

For example:

```text
Route 53
   ↓
sheepeye.shop
   ↓
CloudFront
   ↓
ACM Certificate
   ↓
HTTPS
```

If you later add an ALB, ACM can also provide a certificate for the ALB's HTTPS listener.

---

# ACM and Terraform

ACM certificates can be managed through Terraform.

Conceptually:

```hcl
resource "aws_acm_certificate" "example" {
  domain_name       = "example.com"
  validation_method = "DNS"
}
```

Terraform can also manage the DNS validation records and certificate validation process.

This is useful for infrastructure-as-code projects such as Sheepeye.

---

# Common SAA Scenarios

## Scenario 1: Enable HTTPS on CloudFront

> A company wants to use a custom domain with HTTPS through CloudFront.

Use:

```text
ACM
  ↓
Certificate in us-east-1
  ↓
CloudFront
```

---

## Scenario 2: HTTPS on ALB

> An application is behind an Application Load Balancer and needs HTTPS.

Use:

```text
ACM
  ↓
Certificate
  ↓
ALB HTTPS Listener
  ↓
EC2
```

---

## Scenario 3: Automatic certificate renewal

> The company wants to avoid manually renewing certificates.

Use an ACM-issued certificate and configure it with supported AWS services so ACM can manage renewal.

---

## Scenario 4: Domain ownership validation

> ACM requires proof that the company controls the domain.

Use:

```text
DNS validation
```

or:

```text
Email validation
```

DNS validation is commonly preferred for automation.

---

## Scenario 5: CloudFront certificate in wrong Region

> A certificate was created in `ap-south-1`, but CloudFront cannot use it.

The issue is the certificate Region.

For CloudFront:

```text
ACM certificate
      ↓
us-east-1
```

---

# ACM vs Let's Encrypt

Both can provide TLS certificates, but they serve different operational models.

### ACM

Best integrated with AWS services such as:

* CloudFront
* ALB
* API Gateway

AWS manages much of the certificate lifecycle.

### Let's Encrypt

A widely used external certificate authority that provides publicly trusted certificates.

It can be useful in environments where you manage the certificate lifecycle yourself.

For AWS-native architectures, ACM is often the simpler choice when the certificate is used with supported AWS services.

---

# Key Points

* ACM stands for **AWS Certificate Manager**.
* ACM manages **SSL/TLS certificates**.
* HTTPS normally uses **TCP port 443**.
* ACM supports domain validation through DNS or email.
* DNS validation is commonly preferred for automation.
* ACM can automatically renew eligible ACM-issued certificates used with supported AWS services.
* ACM integrates with services such as **CloudFront and ALB**.
* A CloudFront certificate must be in **`us-east-1`**.
* An ALB can use an ACM certificate for its HTTPS listener.
* TLS termination can occur at the load balancer.
* ACM Private CA is used for private certificate authorities and internal certificates.
* Route 53 handles DNS; ACM handles certificates.
* Security groups control network traffic; ACM does not replace them.
* For SAA, remember:

  * **ACM = certificates**
  * **Route 53 = DNS**
  * **CloudFront = CDN**
  * **ALB = load balancing**
  * **HTTPS = TLS-secured HTTP**
