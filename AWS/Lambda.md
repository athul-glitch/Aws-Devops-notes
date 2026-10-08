# AWS Lambda

## What is AWS Lambda?

AWS Lambda is a **serverless compute service** that runs code without requiring you to manage servers.

You provide the code, and AWS manages:

* Servers
* Operating systems
* Infrastructure
* Scaling
* Availability

Lambda runs your code when it is triggered by an event.

---

## How Lambda Works

A basic Lambda flow looks like:

```text
Event
  |
  ↓
Lambda Function
  |
  ↓
Code executes
  |
  ↓
Result
```

For example:

```text
User uploads file
       |
       ↓
      S3
       |
       ↓
    Lambda
       |
       ↓
Process the file
```

---

## Lambda Function

A Lambda function contains the code that AWS executes.

A function normally includes:

* Code
* Runtime
* Memory configuration
* Timeout
* Environment variables
* IAM execution role
* Trigger/event source

Common Lambda runtimes include:

* Python
* Node.js
* Java
* .NET

Python and Node.js are commonly used for cloud automation and event-driven applications.

---

## Lambda Execution Role

A Lambda function can use an **IAM execution role** to access AWS services.

Example:

```text
Lambda
   |
   ↓
IAM Execution Role
   |
   ↓
IAM Policy
   |
   ↓
S3
```

For example, a Lambda function that reads objects from S3 needs appropriate permissions to access S3.

The role should follow **least privilege**.

---

## Lambda Triggers

Lambda can be triggered by many AWS services.

Examples:

* S3
* API Gateway
* EventBridge
* SNS
* SQS
* CloudWatch Events/EventBridge
* DynamoDB Streams

Example:

```text
S3 Upload
    |
    ↓
Lambda
    |
    ↓
Process Object
```

---

## Lambda and API Gateway

Lambda can be used as the backend for an API.

```text
Client
  |
  ↓
API Gateway
  |
  ↓
Lambda
  |
  ↓
Database / AWS Service
```

API Gateway handles the API request while Lambda executes the backend logic.

This is a common **serverless architecture**.

---

## Lambda and S3

S3 can trigger Lambda when an object is created or changed.

Example:

```text
User
 |
 ↓
S3 Bucket
 |
 | Object Created
 ↓
Lambda
 |
 ↓
Process Object
```

Possible uses:

* Image processing
* File validation
* Data transformation
* Log processing
* Automated workflows

---

## Lambda and SQS

Lambda can process messages from an SQS queue.

```text
Application
    |
    ↓
   SQS
    |
    ↓
 Lambda
    |
    ↓
Process Message
```

This is useful for asynchronous processing.

For example, an application can put tasks into SQS and Lambda can process them without the application waiting for the task to finish.

---

## Lambda and SNS

SNS can also invoke Lambda when a message is published.

```text
Publisher
   |
   ↓
  SNS
   |
   ↓
Lambda
```

This allows event-driven processing.

---

## Lambda Scaling

Lambda automatically scales based on incoming requests.

For example:

```text
10 requests
     ↓
Lambda
     ↓
More concurrent executions

1000 requests
     ↓
Lambda
     ↓
More concurrent executions
```

AWS manages the underlying compute capacity.

However, Lambda has concurrency limits and workloads may need appropriate configuration.

---

## Lambda Statelessness

Lambda functions should generally be designed as **stateless**.

Do not rely on the local execution environment to permanently store application data.

For persistent storage, use services such as:

* S3
* DynamoDB
* RDS
* EFS

Example:

```text
Lambda
 |
 ├── S3
 ├── DynamoDB
 └── RDS
```

---

## Lambda Timeout

Every Lambda invocation has a maximum execution time.

If the function takes longer than its configured timeout, AWS terminates the invocation.

Therefore, Lambda is best suited for workloads that can complete within its execution limits.

Long-running workloads may be better suited to services such as EC2, ECS, or other compute solutions.

---

## Lambda Memory

Lambda memory can be configured for a function.

Increasing memory also provides more CPU resources.

The correct configuration depends on the workload.

---

## Environment Variables

Lambda supports environment variables for configuration.

Example:

```text
DATABASE_HOST=example
ENVIRONMENT=production
```

Environment variables can help separate configuration from application code.

Sensitive information should not simply be stored as plain environment variables. Services such as **AWS Secrets Manager** can be used for sensitive credentials.

---

## Lambda Layers

Lambda Layers allow common code and dependencies to be shared between functions.

Example:

```text
             Layer
        /      |      \
       ↓       ↓       ↓
 Lambda A  Lambda B  Lambda C
```

This can reduce duplication between functions.

---

## Lambda and CloudWatch

Lambda integrates with Amazon CloudWatch.

CloudWatch can be used for:

* Logs
* Metrics
* Errors
* Invocations
* Duration
* Monitoring

A common troubleshooting flow is:

```text
Lambda Error
     |
     ↓
CloudWatch Logs
     |
     ↓
Check error message
     |
     ↓
Fix code/configuration
```

---

## Lambda Pricing Concept

Lambda follows a **pay-for-use** model rather than requiring you to run a server continuously.

You generally pay based on factors such as:

* Number of requests
* Duration
* Allocated memory

This can make Lambda attractive for workloads that do not need continuously running servers.

---

## Lambda vs EC2

| Feature                | Lambda                 | EC2                   |
| ---------------------- | ---------------------- | --------------------- |
| Server management      | AWS manages            | You manage            |
| Infrastructure         | Serverless             | Virtual machine       |
| Scaling                | Automatic              | Configure/manage      |
| Billing                | Usage based            | Instance running time |
| Long-running workloads | Not ideal              | Suitable              |
| OS management          | AWS manages            | You manage            |
| Best for               | Event-driven workloads | Full server control   |

---

## Lambda vs ECS

Lambda is useful for short-lived event-driven functions.

ECS is useful for running containerized applications where you need more control over the application environment.

```text
Event-driven function
        ↓
      Lambda

Containerized application
        ↓
       ECS
```

---

## Lambda and Sheepeye

Lambda could be used in Sheepeye for small event-driven tasks.

For example:

```text
Customer Booking
       |
       ↓
Application
       |
       ↓
     SNS
       |
       ↓
    Lambda
       |
       ↓
Notification / Processing
```

However, there is no need to add Lambda just to make the project use more AWS services.

The goal should be to use Lambda where it provides a real architectural benefit.

---

## Key Points

* Lambda is a **serverless compute service**.
* You provide code; AWS manages the underlying servers.
* Lambda runs code in response to events.
* Lambda supports runtimes such as Python, Node.js, Java, and .NET.
* IAM execution roles control what a Lambda function can access.
* Lambda can be triggered by services such as S3, API Gateway, SQS, SNS, and EventBridge.
* Lambda automatically scales with incoming requests.
* Lambda functions should generally be stateless.
* Persistent data should be stored in services such as S3, DynamoDB, RDS, or EFS.
* CloudWatch provides Lambda logs and monitoring.
* Lambda is best suited for event-driven and relatively short-running workloads.
* EC2 provides more control but requires more infrastructure management.
