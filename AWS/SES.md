# Amazon Simple Email Service (SES)

## What is Amazon SES?

Amazon Simple Email Service (**SES**) is a managed AWS service used to **send and receive email**.

Applications can use SES to send:

* Transactional emails
* Notifications
* Verification emails
* Password reset emails
* Marketing emails
* Alerts and reports

```text id="j3r7qx"
Application
     |
     ↓
    SES
     |
     ↓
Recipient Email
```

---

## Why Use SES?

Instead of running and maintaining your own email server, an application can use SES to send email through AWS.

Example:

```text id="v6c2m8"
Sheepeye
   |
   ↓
AWS SES
   |
   ↓
Customer Email
```

SES handles the underlying email delivery infrastructure.

---

# Transactional Email

Transactional emails are automatically generated in response to an application event.

Examples:

* Booking confirmation
* Account verification
* Password reset
* Payment confirmation
* Order confirmation

Example:

```text id="4x8n2q"
Customer creates booking
        ↓
Application
        ↓
SES
        ↓
Confirmation Email
```

---

# SES Components

Important SES concepts include:

* Verified identities
* Email addresses
* Domains
* Sending authorization
* Email sending
* Reputation
* Suppression
* Monitoring

---

# Verified Identity

Before sending email through SES, you generally need to verify the identity you will send from.

An identity can be:

* An email address
* A domain

Example:

```text id="r5m8v1"
Verified:
noreply@example.com
```

or:

```text id="t2p7k4"
Verified Domain:
example.com
```

---

# Email Address Verification

You can verify an individual email address.

Example:

```text id="c9w3n6"
noreply@example.com
       ↓
      SES
       ↓
Verification email
```

The owner confirms the address before it can be used according to the SES account's sending configuration.

This is particularly useful when testing SES.

---

# Domain Verification

For production applications, verifying the domain is generally more practical.

Example:

```text id="m4q8s2"
example.com
     ↓
   SES
     ↓
Verified domain
```

Domain verification can be performed using DNS records.

---

# DNS Records and SES

SES can use DNS records to verify domain ownership and configure email authentication.

Common email authentication mechanisms include:

* SPF
* DKIM
* DMARC

These help receiving email providers verify that messages are legitimately associated with the sending domain.

---

# SPF

**Sender Policy Framework (SPF)** is an email authentication mechanism.

It allows a domain to specify which servers/services are authorized to send email for that domain.

Conceptually:

```text id="k7d2p9"
Receiving Mail Server
        |
        ↓
Check SPF
        |
        ↓
Is sender authorized?
```

---

# DKIM

**DomainKeys Identified Mail (DKIM)** uses cryptographic signatures to help verify that an email was authorized by the sending domain and that the message was not altered after signing.

Conceptually:

```text id="a6m3x8"
SES
 ↓
DKIM Signature
 ↓
Email
 ↓
Recipient Mail Server
```

---

# DMARC

**Domain-based Message Authentication, Reporting, and Conformance (DMARC)** builds on SPF and DKIM.

It allows a domain owner to define how receiving mail systems should handle messages that fail authentication checks.

Conceptually:

```text id="p4n8v2"
Email
  ↓
SPF / DKIM
  ↓
DMARC Policy
  ↓
Accept / Quarantine / Reject
```

---

# SES Sending

Applications can send email through SES using AWS APIs or supported interfaces.

Conceptually:

```text id="w3r9m5"
Application
    |
    ↓
SES API
    |
    ↓
Email
    |
    ↓
Recipient
```

---

# SES and IAM

IAM can control which AWS resources or applications are allowed to use SES.

For example, an EC2 application can use an IAM role to obtain permission to send email without storing long-term AWS access keys on the server.

```text id="y6q2c8"
EC2
 |
 | IAM Role
 ↓
SES
 |
 ↓
Email
```

This follows the AWS best practice of using roles instead of embedding credentials in applications.

---

# SES and Lambda

Lambda can send email through SES.

Example:

```text id="h8m4s1"
Application Event
       ↓
Lambda
       ↓
SES
       ↓
Email
```

For example, a Lambda function could send a confirmation email after a successful booking.

---

# SES and SNS

SES and SNS both support notification-related architectures, but they have different purposes.

### SNS

SNS is mainly a **notification and pub/sub service**.

```text id="2c8v7q"
SNS
 ├── Email
 ├── SMS
 ├── SQS
 └── Lambda
```

### SES

SES is specifically designed for **email sending and receiving**.

```text id="s9m3x6"
Application
     ↓
    SES
     ↓
Email
```

Simple rule:

```text id="b5k8r2"
Need application email delivery?
        ↓
       SES

Need general notification/fan-out?
        ↓
       SNS
```

---

# SES vs SNS Email

SNS can deliver notifications through email subscriptions.

However, SES provides more control for application email workflows and is specifically designed for email sending.

For example:

```text id="n4q7w1"
Booking System
      ↓
     SES
      ↓
"Your booking is confirmed"
```

For application-generated transactional emails, SES is usually the more appropriate service.

---

# SES Sandbox

New SES accounts may initially operate in the **SES sandbox**.

Sandbox restrictions can limit who you can send email to and how much email you can send.

For production use, you may need to request production access.

Conceptually:

```text id="x8c3m5"
New SES Account
       ↓
     Sandbox
       ↓
Request production access
       ↓
Production
```

The exact sending limits depend on the account and AWS configuration.

---

# Sending Quotas

SES has sending quotas and limits.

These can include:

* Maximum messages that can be sent within a period
* Sending rate
* Account-specific limits

Production workloads should monitor sending volume and SES account limits.

---

# Bounce

A **bounce** occurs when an email cannot be delivered to the recipient.

Examples:

* Invalid email address
* Non-existent domain
* Recipient mailbox unavailable
* Other delivery failures

Conceptually:

```text id="d6p2v9"
SES
 ↓
Email
 ↓
Recipient
 ↓
Delivery Failure
 ↓
Bounce
```

---

# Complaint

A **complaint** occurs when a recipient reports an email as unwanted or spam.

High complaint rates can negatively affect sender reputation.

Therefore, applications should send relevant emails only to appropriate recipients.

---

# SES Reputation

Email providers evaluate sender reputation.

Important factors include:

* Bounce rate
* Complaint rate
* Sending behavior
* Email quality

Poor sending practices can negatively affect deliverability.

---

# Suppression List

SES can maintain suppression information for addresses that have experienced certain delivery problems or complaints.

This helps prevent repeatedly sending messages to problematic recipients.

Example:

```text id="r7m4x9"
Email fails repeatedly
        ↓
Suppression
        ↓
Application avoids unnecessary sends
```

---

# SES Monitoring

SES can be monitored using AWS services such as CloudWatch.

Useful information can include:

* Sends
* Bounces
* Complaints
* Delivery-related metrics

Example:

```text id="f3k8q2"
SES
 ↓
CloudWatch
 ↓
Metrics / Monitoring
```

---

# SES Event Publishing

SES can publish email sending events for monitoring and processing.

Events can include:

* Send
* Delivery
* Bounce
* Complaint
* Reject

These events can be integrated with other AWS services depending on the architecture.

---

# SES and EventBridge

SES events can also participate in event-driven architectures.

Conceptually:

```text id="c5n9w4"
SES Event
    ↓
EventBridge
    ↓
Rule
    ↓
Lambda / SQS / Other Target
```

This can be useful for automated processing of email delivery events.

---

# SES and S3

SES can work with S3 in email receiving architectures.

Conceptually:

```text id="u8m2p6"
Incoming Email
      ↓
     SES
      ↓
     S3
      ↓
Stored Email
```

This allows incoming email data to be stored in S3 for further processing.

---

# SES Receiving Email

SES is not only for sending email.

It can also be configured to **receive email** for supported domains and then route the incoming email to other AWS services.

For example:

```text id="e7q4x1"
Incoming Email
      ↓
     SES
      ↓
     S3
      ↓
Lambda / Application
```

This can be useful for automated email processing.

---

# SES and Sheepeye

SES could be useful in your Sheepeye project for transactional emails.

For example:

```text id="m8c3v7"
Customer
   |
   ↓
Sheepeye Booking
   |
   ↓
Application
   |
   ↓
SES
   |
   ↓
Booking Confirmation
```

Possible emails:

* Booking confirmation
* Booking status update
* Service reminder
* Admin notification

Your existing SNS notification setup can remain as it is.

SES would be an optional future enhancement if you want to demonstrate transactional email delivery.

---

# SES and IAM Role in Sheepeye

If Sheepeye runs on EC2, the application should ideally use an **IAM role attached to the EC2 instance** rather than storing AWS access keys inside the application.

Example:

```text id="q6v1n8"
Sheepeye EC2
     |
     | IAM Role
     ↓
    SES
     |
     ↓
Customer Email
```

This is a good cloud security practice.

---

# Common SAA Scenarios

## Scenario 1: Send application emails

> An application needs to send booking confirmations and password reset emails.

Use:

```text id="j5r8c2"
Amazon SES
```

---

## Scenario 2: Verify a sending domain

> A company wants to send email using its own domain.

Use:

```text id="v9m3x6"
SES
 +
DNS verification
```

---

## Scenario 3: Avoid storing AWS credentials

> An EC2 application needs permission to send emails through SES.

Use:

```text id="n2k7p4"
EC2
 ↓
IAM Role
 ↓
SES
```

Do not store long-term AWS access keys in the application.

---

## Scenario 4: Monitor email delivery

> The company wants to monitor bounces, complaints and email delivery activity.

Use:

```text id="s4q8w1"
SES
 ↓
CloudWatch / Event-driven monitoring
```

---

## Scenario 5: Transactional email

> An e-commerce application needs to send order confirmation emails after customers place orders.

Use:

```text id="c7m5x9"
Application
    ↓
SES
    ↓
Customer
```

---

# Key Points

* SES stands for **Amazon Simple Email Service**.
* SES is a managed service for **sending and receiving email**.
* Common uses include transactional emails, notifications and verification emails.
* Email addresses or domains can be verified before sending.
* DNS validation is commonly used for domain verification.
* SPF, DKIM and DMARC improve email authentication and deliverability.
* New SES accounts may initially operate in the **sandbox**.
* Production workloads may require production access.
* Bounces indicate delivery failures.
* Complaints indicate recipients reported messages as unwanted.
* Sender reputation is important for email deliverability.
* IAM can control access to SES.
* EC2 applications should use IAM roles rather than hard-coded AWS credentials.
* SES can integrate with Lambda, S3, CloudWatch and EventBridge.
* **SES = application email service.**
* **SNS = general notification/pub-sub service.**
* For SAA, think of SES when the requirement is **reliable application email sending/receiving**.
