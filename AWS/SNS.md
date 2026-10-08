# Amazon Simple Notification Service (SNS)

## What is Amazon SNS?

Amazon Simple Notification Service (**SNS**) is a managed AWS service used to **send notifications and distribute messages to multiple subscribers**.

SNS is commonly used for:

* Application notifications
* Pub/sub messaging
* Fan-out architectures
* Alerts
* Email and SMS notifications
* Sending messages to SQS queues
* Triggering Lambda functions

Conceptually:

```text
Publisher
    |
    ↓
  SNS Topic
   / | \
  ↓  ↓  ↓
 SQS Lambda Email
```

---

# Why Use SNS?

SNS helps applications send one message to multiple destinations without directly connecting the publisher to every subscriber.

Without SNS:

```text
Application
 ├──→ Email
 ├──→ SQS
 ├──→ Lambda
 └──→ SMS
```

With SNS:

```text
Application
     |
     ↓
 SNS Topic
  /  |  \
 ↓   ↓   ↓
SQS Lambda Email
```

This makes the architecture more loosely coupled.

---

# SNS Topic

A **topic** is a logical access point used to distribute messages to subscribers.

Example:

```text
Sheepeye Booking
       ↓
   SNS Topic
```

Subscribers can then receive messages published to that topic.

---

# Publisher

A publisher is the application or service that sends a message to an SNS topic.

Example:

```text
Sheepeye Application
       ↓
    Publish
       ↓
  SNS Topic
```

The publisher does not need to know every subscriber.

---

# Subscriber

A subscriber is a destination that receives messages from an SNS topic.

Common SNS subscription endpoints include:

* SQS
* Lambda
* HTTP/HTTPS
* Email
* SMS
* Other supported AWS destinations

Example:

```text
SNS Topic
   |
   ├── SQS
   ├── Lambda
   └── Email
```

---

# Pub/Sub Model

SNS follows a **publish/subscribe (pub/sub)** model.

```text
Publisher
    |
    ↓
 SNS Topic
    |
    ├──→ Subscriber 1
    ├──→ Subscriber 2
    └──→ Subscriber 3
```

The publisher sends the message to the topic instead of directly sending it to each subscriber.

---

# Fan-Out Architecture

One of the most important SNS concepts for the AWS SAA exam is **fan-out**.

Fan-out means sending one message to multiple destinations.

Example:

```text
             SNS Topic
            /    |    \
           ↓     ↓     ↓
         SQS   Lambda  SQS
          ↓      ↓      ↓
       Worker  Process  Worker
```

A single published message can be delivered to multiple subscribed endpoints.

---

# SNS + SQS Fan-Out

A common AWS architecture uses SNS together with SQS.

Example:

```text
                  SNS Topic
                 /         \
                ↓           ↓
             SQS Queue   SQS Queue
                ↓           ↓
          Application A  Application B
```

This is useful when multiple independent applications need to process the same event.

---

# Why Use SNS + SQS?

SNS provides:

* Message distribution
* Fan-out
* Pub/sub

SQS provides:

* Message queueing
* Decoupling
* Buffering
* Independent message processing

Together:

```text
SNS → Distribute
SQS → Store / Buffer
```

---

# SNS + Lambda

SNS can invoke Lambda when a message is published.

```text
Application
    ↓
SNS Topic
    ↓
Lambda
    ↓
Processing
```

Example:

A booking application publishes a booking event.

```text
Booking Created
      ↓
SNS
      ↓
Lambda
      ↓
Send / Process Notification
```

---

# SNS + Email

SNS can send notifications through email subscriptions.

Example:

```text
SNS Topic
    ↓
Email Subscription
    ↓
Administrator
```

This is useful for simple notification scenarios.

For dedicated transactional application email workflows, **SES** is generally the more appropriate service.

---

# SNS + SMS

SNS can also support SMS messaging.

Example:

```text
Application
    ↓
SNS
    ↓
SMS
    ↓
Customer
```

This can be useful for notification scenarios where supported by the AWS service and destination.

---

# SNS Message

A message is the data published to an SNS topic.

Example:

```text
{
  "booking_id": "12345",
  "customer": "John",
  "status": "confirmed"
}
```

SNS then distributes the message to its subscribers.

---

# SNS Subscription

A subscription connects a destination to an SNS topic.

Conceptually:

```text
SNS Topic
    |
    ↓
Subscription
    |
    ↓
Endpoint
```

Example:

```text
SNS Topic
   |
   ├── SQS Queue
   ├── Lambda
   └── Email
```

---

# Message Filtering

SNS supports **message filtering policies**.

This allows subscribers to receive only messages matching specific attributes.

Example:

```text
SNS Topic
   |
   ├── Billing Subscriber
   |      ↓
   |   billing events
   |
   └── Booking Subscriber
          ↓
       booking events
```

The topic can distribute different messages to different subscribers based on message attributes and subscription filter policies.

---

# SNS Standard Topics

Standard SNS topics are designed for high-throughput message distribution.

They are suitable when extremely high throughput and scalable fan-out are more important than strict ordering.

Example:

```text
Application
    ↓
Standard SNS Topic
    ↓
Multiple Subscribers
```

---

# SNS FIFO Topics

SNS also supports **FIFO topics** for workloads that require:

* Message ordering
* Exactly-once processing semantics when used with supported FIFO integrations

FIFO topics are useful when the order of related messages is important.

Example:

```text
Message 1
   ↓
Message 2
   ↓
Message 3
```

The subscriber can process the ordered sequence according to the FIFO configuration.

---

# Standard vs FIFO

| Feature            | Standard                     | FIFO                       |
| ------------------ | ---------------------------- | -------------------------- |
| Throughput         | Very high                    | Lower than Standard        |
| Ordering           | Not guaranteed               | Ordered                    |
| Duplicate handling | At-least-once delivery model | Stronger duplicate control |
| Use case           | General notifications        | Ordered event processing   |

For SAA questions, think:

```text
Need maximum scalable fan-out?
        ↓
Standard SNS

Need ordering?
        ↓
FIFO SNS
```

---

# SNS Encryption

SNS supports encryption using AWS Key Management Service (**KMS**).

Conceptually:

```text
Publisher
    ↓
SNS Topic
    ↓
KMS Encryption
    ↓
Encrypted Message
```

Encryption is useful when messages contain sensitive information.

IAM permissions must also be configured correctly when using encrypted SNS resources.

---

# SNS Access Control

IAM policies and SNS topic policies can control access to SNS resources.

They can help determine:

* Who can publish
* Who can subscribe
* Who can manage the topic

Example:

```text
Application
    ↓
IAM Permission
    ↓
sns:Publish
    ↓
SNS Topic
```

Use least privilege when granting SNS permissions.

---

# SNS Cross-Account Access

SNS topics can be used in cross-account architectures.

For example:

```text
AWS Account A
     |
     ↓
 SNS Topic
     |
     ↓
AWS Account B
     |
     ↓
Subscriber
```

Topic policies can be used to control cross-account access.

---

# SNS and EventBridge

SNS and EventBridge can both be used in event-driven architectures, but they serve different purposes.

### SNS

Best known for:

* Pub/sub
* Fan-out
* Notifications

```text
SNS Topic
 / | \
↓  ↓  ↓
A  B  C
```

### EventBridge

Best known for:

* Event buses
* Event routing
* Event patterns
* Scheduled events
* AWS service events

```text
Event
  ↓
EventBridge
  ↓
Rule
  ↓
Target
```

Simple rule:

```text
SNS → Fan-out / notifications

EventBridge → Event routing / automation
```

---

# SNS vs SQS

These services are often used together but have different purposes.

### SNS

**Push-based pub/sub**

```text
Publisher
    ↓
SNS
    ↓
Subscribers
```

### SQS

**Queue-based message processing**

```text
Producer
   ↓
SQS Queue
   ↓
Consumer
```

Simple rule:

```text
SNS → distribute messages

SQS → queue messages
```

---

# SNS vs SES

SNS and SES both involve email-related functionality but are designed for different purposes.

### SNS

General notification service.

```text
SNS
 ↓
Notification
```

### SES

Dedicated email service.

```text
SES
 ↓
Application Email
```

For example:

```text
System Alert
     ↓
SNS
     ↓
Admin Email
```

versus:

```text
Booking Confirmation
       ↓
      SES
       ↓
Customer
```

---

# SNS and CloudWatch

CloudWatch alarms can publish notifications to SNS.

Example:

```text
EC2
 ↓
CloudWatch
 ↓
Alarm
 ↓
SNS
 ↓
Email / Other Subscriber
```

This is a very common AWS monitoring architecture.

Example:

> If EC2 CPU usage remains above a threshold, notify the administrator.

Architecture:

```text
EC2
 ↓
CloudWatch Metric
 ↓
CloudWatch Alarm
 ↓
SNS Topic
 ↓
Email
```

---

# SNS and CloudTrail

CloudTrail records AWS API activity.

SNS can be used to notify systems or administrators about selected events through an event-driven architecture.

For example:

```text
CloudTrail
    ↓
EventBridge
    ↓
SNS
    ↓
Notification
```

This can be used for security monitoring.

---

# SNS and Sheepeye

SNS is already relevant to your Sheepeye project.

Your existing architecture can be represented as:

```text
Customer
   ↓
Sheepeye Booking
   ↓
Application
   ↓
SNS Topic
   ↓
Notification
```

Your SNS topic display name is:

```text
sheepeye booking
```

SNS can be used for notifications such as:

* New booking notification
* Admin alert
* Booking status notification
* System alerts

---

# Sheepeye Future Fan-Out

Later, you could expand the architecture:

```text
                 Sheepeye
                    |
                    ↓
                SNS Topic
               /    |    \
              ↓     ↓     ↓
            Email   SQS  Lambda
              ↓      ↓     ↓
           Admin   Worker  Automation
```

You don't need to build this entire architecture now.

Your current SNS setup is enough to demonstrate that you understand AWS notifications and event-driven architecture.

---

# Terraform and SNS

SNS topics can be created using Terraform.

Conceptually:

```text
Terraform
    ↓
SNS Topic
    ↓
Subscription
```

Example resource structure:

```text
sns.tf
```

Typical Terraform resources include:

```text
aws_sns_topic
aws_sns_topic_subscription
```

Terraform can therefore manage the SNS infrastructure as code.

---

# Common SAA Scenarios

## Scenario 1: Send one event to multiple systems

> A company wants one application event to be delivered to multiple independent applications.

Use:

```text
SNS Fan-Out
```

---

## Scenario 2: Multiple consumers need the same message

> Application A and Application B both need to process every order event independently.

Use:

```text
SNS
 / \
↓   ↓
SQS SQS
```

---

## Scenario 3: Queue messages for later processing

> A consumer may be temporarily unavailable and messages must wait until it can process them.

Use:

```text
SQS
```

SNS alone is not primarily a durable message queue.

---

## Scenario 4: Notify an administrator when CPU is high

Use:

```text
EC2
 ↓
CloudWatch
 ↓
Alarm
 ↓
SNS
 ↓
Email
```

---

## Scenario 5: Messages must maintain order

Use:

```text
SNS FIFO Topic
```

when the workload and subscription path support the required FIFO semantics.

---

## Scenario 6: Route events based on event patterns

> Different AWS events need to be routed to different targets based on their contents.

Consider:

```text
EventBridge
```

rather than using SNS as the primary event-routing engine.

---

# Key Points

* **SNS = Simple Notification Service.**
* SNS is a managed **pub/sub and notification** service.
* A **topic** is the central distribution point.
* Applications publish messages to topics.
* Subscribers receive messages from topics.
* SNS is commonly used for **fan-out architectures**.
* SNS can integrate with SQS, Lambda, email and other destinations.
* SNS + SQS is a common pattern for distributing and independently processing messages.
* Standard SNS topics are designed for high-throughput distribution.
* FIFO SNS topics are used when ordering requirements exist.
* SNS supports message filtering policies.
* SNS supports encryption using KMS.
* IAM and topic policies control access.
* CloudWatch alarms can publish notifications to SNS.
* **SNS → distribute/notify.**
* **SQS → queue/process.**
* **SES → application email.**
* **EventBridge → event routing/automation.**
* For SAA, the most important SNS concept is **fan-out**.
