# Amazon Simple Queue Service (SQS)

## What is Amazon SQS?

Amazon Simple Queue Service (**SQS**) is a fully managed messaging service used to **decouple applications**.

Instead of one application directly calling another application, it can place a message into an SQS queue.

```text id="6k5j3m"
Producer
   |
   ↓
SQS Queue
   |
   ↓
Consumer
```

The producer sends a message to the queue.

The consumer retrieves and processes the message.

---

## Why Use SQS?

Without a queue:

```text id="h1b7z8"
Application A
     |
     ↓
Application B
```

If Application B is unavailable, Application A may also be affected.

With SQS:

```text id="t0n3yx"
Application A
     |
     ↓
SQS Queue
     |
     ↓
Application B
```

Application A can continue sending messages even if Application B is temporarily unavailable.

This creates **loose coupling** between the applications.

---

## Producer and Consumer

### Producer

The producer sends messages to the queue.

```text id="v8s4q0"
Producer
   |
   ↓
SQS
```

Examples:

* Web application
* Lambda function
* EC2 application
* Backend service

### Consumer

The consumer retrieves messages from the queue and processes them.

```text id="z5c6k1"
SQS
 |
 ↓
Consumer
```

Examples:

* EC2
* Lambda
* Container application
* Background worker

---

## Basic SQS Architecture

```text id="m2v7q9"
User
 |
 ↓
Web Application
 |
 ↓
SQS Queue
 |
 ├── Message 1
 ├── Message 2
 └── Message 3
       |
       ↓
   Worker / Consumer
       |
       ↓
    Processing
```

The queue acts as a buffer between the producer and consumer.

---

## Message

A message contains information that the consumer needs to process.

Example:

```json id="b4m9xs"
{
  "bookingId": "12345",
  "customer": "John",
  "service": "Car Wash"
}
```

The producer sends this message to SQS.

The consumer retrieves it and performs the required processing.

---

## Queue

The queue temporarily stores messages until consumers process them.

Example:

```text id="r3y6w2"
SQS Queue
│
├── Message A
├── Message B
├── Message C
└── Message D
```

Messages can remain in the queue until they are successfully processed or expire according to the queue configuration.

---

# Standard Queue

An SQS **Standard Queue** provides very high throughput and **at-least-once delivery**.

Important characteristics:

* Very high scalability
* At-least-once delivery
* Best-effort ordering
* Possible duplicate messages

Because duplicate delivery can occur, consumers should ideally be **idempotent**.

---

## FIFO Queue

**FIFO** means **First-In, First-Out**.

FIFO queues are designed when message ordering and stronger duplicate-handling requirements are important.

Example:

```text id="8m5c4p"
Message 1
   ↓
Message 2
   ↓
Message 3

Processing order:
1 → 2 → 3
```

FIFO queues support features such as:

* Message ordering
* Message deduplication

Use FIFO when the application needs ordering guarantees.

---

## Standard vs FIFO

| Feature            | Standard             | FIFO                  |
| ------------------ | -------------------- | --------------------- |
| Throughput         | Very high            | Lower than Standard   |
| Ordering           | Best effort          | Ordered               |
| Duplicate delivery | Possible             | Deduplication support |
| Main use           | High-scale messaging | Ordered processing    |

Simple rule:

```text id="j7s9n1"
Need maximum throughput?
        ↓
     Standard

Need ordering?
        ↓
       FIFO
```

---

## Visibility Timeout

When a consumer receives a message, SQS temporarily hides that message from other consumers.

This period is called the **visibility timeout**.

Example:

```text id="n8v4c2"
Message
   |
   ↓
Consumer receives message
   |
   ↓
Visibility Timeout
   |
   ↓
Message hidden temporarily
```

The consumer should process the message and delete it before the visibility timeout expires.

---

## Why Visibility Timeout Matters

Suppose:

```text id="x7k2d9"
Visibility Timeout = 60 seconds
```

The consumer receives a message.

If processing succeeds:

```text id="f6p1r3"
Process
  ↓
Delete Message
```

If processing takes too long and the message is not deleted, it can become visible again.

```text id="y4w8m2"
Timeout expires
      ↓
Message visible again
      ↓
Another consumer can process it
```

Therefore, visibility timeout should be configured appropriately for the expected processing time.

---

## Delete Message

SQS does not automatically consider a message permanently processed merely because a consumer received it.

After successful processing, the consumer deletes the message.

```text id="q8s3v5"
SQS
 |
 ↓
Receive Message
 |
 ↓
Process
 |
 ↓
Success
 |
 ↓
Delete Message
```

If processing fails, the message can become visible again after the visibility timeout.

---

## Dead-Letter Queue

A **Dead-Letter Queue (DLQ)** stores messages that repeatedly fail processing.

Example:

```text id="a6m4k9"
Main Queue
    |
    ↓
Consumer
    |
    ├── Success → Delete
    |
    └── Failure
          |
          ↓
     Retry Attempts
          |
          ↓
         DLQ
```

A DLQ helps isolate problematic messages for investigation.

---

## Redrive Policy

A queue can be configured with a maximum number of receive attempts.

Example:

```text id="p7c5x2"
Message
 ↓
Attempt 1 → Fail
 ↓
Attempt 2 → Fail
 ↓
Attempt 3 → Fail
 ↓
DLQ
```

This prevents a permanently failing message from repeatedly blocking or consuming processing capacity.

---

## Long Polling

SQS supports **long polling**.

With short polling, a consumer repeatedly checks the queue.

With long polling, the request can wait for messages to become available.

Conceptually:

```text id="s3h7m1"
Consumer
   |
   ↓
Wait for message
   |
   ↓
Message arrives
   |
   ↓
Return message
```

Long polling can reduce unnecessary empty responses and improve efficiency.

---

## SQS and Auto Scaling

SQS can work with Auto Scaling to process variable workloads.

Example:

```text id="j4m8n6"
Producers
   |
   ↓
SQS Queue
   |
   ↓
EC2 Worker Fleet
   |
   ↓
Auto Scaling
```

If the queue grows because many messages are waiting, more workers can be launched.

If the queue becomes smaller, the number of workers can be reduced.

CloudWatch metrics can be used as scaling signals.

---

## SQS and Lambda

Lambda can consume messages from SQS.

Example:

```text id="c9x5r1"
Application
    |
    ↓
SQS
    |
    ↓
Lambda
    |
    ↓
Processing
```

Lambda can automatically poll the queue and invoke functions to process messages.

This is useful for serverless asynchronous workloads.

---

## SQS and SNS

SQS and SNS are commonly used together.

### SNS

SNS is primarily a **publish/subscribe messaging service**.

### SQS

SQS is a **queue** that stores messages for consumers to process.

Example:

```text id="n5d8q2"
                 SNS
                  |
          ┌───────┼───────┐
          ↓       ↓       ↓
       SQS-A    SQS-B   Lambda
          |
          ↓
       Consumer
```

SNS can fan out a message to multiple subscribers.

Each SQS queue can then process the message independently.

---

## SNS vs SQS

| Feature         | SNS                                | SQS                           |
| --------------- | ---------------------------------- | ----------------------------- |
| Model           | Pub/Sub                            | Queue                         |
| Main purpose    | Fan-out notifications/messages     | Decouple and buffer workloads |
| Message storage | Not a traditional processing queue | Queue storage                 |
| Consumers       | Multiple subscribers               | Consumers poll/process queue  |
| Common use      | Notifications, fan-out             | Background jobs               |

Simple rule:

```text id="w2k7m4"
Need fan-out?
    ↓
   SNS

Need queue/buffer?
    ↓
   SQS
```

---

## SQS and CloudWatch

CloudWatch can monitor SQS metrics.

One important metric is:

**ApproximateNumberOfMessagesVisible**

This represents the approximate number of messages currently available for processing.

Example:

```text id="x1c6v8"
Queue size increasing
        ↓
CloudWatch
        ↓
Scaling decision
        ↓
More consumers
```

This can help identify whether consumers are keeping up with incoming messages.

---

## SQS and Security

SQS supports access control using AWS IAM policies and queue policies.

You can control:

* Who can send messages
* Who can receive messages
* Who can delete messages
* Who can manage the queue

Example actions include:

```text
sqs:SendMessage
sqs:ReceiveMessage
sqs:DeleteMessage
```

---

## SQS Encryption

SQS supports encryption at rest.

AWS KMS can be used for encryption.

Conceptually:

```text id="e4j9s2"
Message
   |
   ↓
SQS
   |
   ↓
KMS Encryption
```

This helps protect messages stored in the queue.

---

## SQS and Application Decoupling

One of the most important reasons to use SQS is **decoupling**.

Without SQS:

```text id="r8m3x1"
Application A
      |
      ↓
Application B
```

Application A depends directly on Application B.

With SQS:

```text id="k6p4v9"
Application A
      |
      ↓
     SQS
      |
      ↓
Application B
```

Now the applications can operate more independently.

---

## SQS as a Buffer

SQS can absorb temporary traffic spikes.

Example:

```text id="d5x8m3"
Normal traffic
     ↓
Queue = 100 messages

Traffic spike
     ↓
Queue = 10,000 messages

Workers process messages
     ↓
Queue decreases
```

Instead of immediately overwhelming the backend, the queue temporarily holds the workload.

---

## SQS and Sheepeye

SQS could be useful in a future Sheepeye architecture.

For example, suppose booking notifications require background processing.

```text id="u8m2q5"
Customer
   |
   ↓
Sheepeye
   |
   ↓
SQS
   |
   ↓
Worker / Lambda
   |
   ├── Process booking
   └── Send notification
```

However, you **do not need to add SQS to Sheepeye just for portfolio purposes**.

Your current SNS notification setup is simpler and sufficient for demonstrating event/notification integration.

---

## SQS in SAA Scenarios

SQS is commonly the correct choice when a question mentions:

* Decoupling applications
* Asynchronous processing
* Buffering traffic spikes
* Background jobs
* Queuing requests
* Retry processing
* Dead-letter queues
* Worker applications
* Reliable message processing

### Example

> A web application receives a large number of image-processing requests. Image processing is slow and should not delay the user's request.

A suitable architecture is:

```text id="b9n4x7"
User
 |
 ↓
Web Application
 |
 ↓
SQS
 |
 ↓
Worker / Lambda
 |
 ↓
Image Processing
```

The web application can accept the request while processing happens asynchronously.

---

## Standard Queue Scenario

Use a Standard Queue when:

* Very high throughput is required
* Exact ordering is not important
* The application can handle possible duplicate deliveries

Example:

```text id="q7m2s4"
Millions of jobs
      ↓
Standard SQS
      ↓
Workers
```

---

## FIFO Scenario

Use FIFO when:

* Message order matters
* Duplicate processing needs stronger controls
* The application requires ordered processing

Example:

```text id="c4v8n1"
Order 1
Order 2
Order 3
   ↓
FIFO Queue
   ↓
Process in order
```

---

## Key Points

* SQS is a managed message queue.
* SQS is primarily used for **decoupling applications**.
* Producers send messages to queues.
* Consumers process messages from queues.
* Standard queues provide very high throughput and at-least-once delivery.
* FIFO queues provide ordered processing and deduplication support.
* Visibility timeout temporarily hides a message after it is received.
* Successfully processed messages should be deleted.
* Dead-Letter Queues isolate repeatedly failing messages.
* Long polling can reduce unnecessary empty responses.
* SQS works well with EC2, Lambda and Auto Scaling.
* SNS and SQS are often combined for fan-out architectures.
* **SNS = publish/fan-out.**
* **SQS = queue/buffer/decouple.**
* SQS supports encryption using KMS.
* For SAA, remember SQS primarily as the solution for **asynchronous processing, buffering and decoupling**.
