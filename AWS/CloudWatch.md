# Amazon CloudWatch

## What is CloudWatch?

Amazon CloudWatch is an AWS monitoring and observability service.

It helps you monitor:

* AWS resources
* Applications
* Servers
* Performance
* Logs
* Events
* Alarms

A simple view:

```text
AWS Resources
     |
     ↓
CloudWatch
     |
 ┌───┼────────┐
 ↓   ↓        ↓
Metrics Logs  Alarms
```

---

## CloudWatch Metrics

Metrics are numerical measurements collected over time.

Examples:

* EC2 CPU utilization
* EC2 network traffic
* RDS CPU utilization
* RDS database connections
* Lambda invocations
* Lambda duration

Example:

```text
EC2 CPU Utilization

10% → 25% → 60% → 90%
```

A high CPU value may indicate that an application or server needs investigation.

---

## CloudWatch Logs

CloudWatch Logs stores and provides access to log data from applications and AWS services.

Example:

```text
Application
     |
     ↓
Application Logs
     |
     ↓
CloudWatch Logs
```

Logs can help identify:

* Application errors
* Failed requests
* Authentication problems
* Configuration issues
* Service failures

---

## Log Groups and Log Streams

CloudWatch Logs uses:

### Log Group

A collection of related log streams.

Example:

```text
/aws/lambda/sheepeye-function
```

### Log Stream

A sequence of log events from a particular source.

Conceptually:

```text
Log Group
   |
   ├── Log Stream 1
   ├── Log Stream 2
   └── Log Stream 3
```

---

## CloudWatch Alarms

CloudWatch Alarms monitor metrics and perform actions when conditions are met.

Example:

```text
EC2 CPU > 80%
       |
       ↓
CloudWatch Alarm
       |
       ↓
SNS Notification
       |
       ↓
Administrator
```

An alarm can be configured with conditions such as:

```text
CPU > 80%
for 5 minutes
```

The alarm can then enter an alarm state and trigger an action.

---

## CloudWatch and SNS

CloudWatch Alarms can send notifications through SNS.

Example:

```text
EC2
 |
 ↓
CloudWatch Metric
 |
 ↓
CloudWatch Alarm
 |
 ↓
SNS
 |
 ↓
Email / Notification
```

This is a common AWS monitoring architecture.

---

## CloudWatch Agent

The CloudWatch Agent can collect additional system-level information from EC2 instances.

For example:

* Memory utilization
* Disk utilization
* Additional logs
* System metrics

Some basic EC2 metrics are available automatically, but additional operating-system-level metrics may require the CloudWatch Agent.

---

## CloudWatch Events and EventBridge

AWS services can generate events when something happens.

Modern AWS event-driven architectures commonly use **Amazon EventBridge** for event routing.

Example:

```text
AWS Event
    |
    ↓
EventBridge
    |
    ↓
Lambda
```

CloudWatch still provides monitoring and metrics, while EventBridge is used for event-driven routing.

---

## CloudWatch Dashboards

CloudWatch Dashboards allow multiple metrics to be displayed together.

Example:

```text
Sheepeye Infrastructure Dashboard

EC2 CPU          35%
Network In       2 MB
Network Out      5 MB
RDS Connections  12
```

This provides a central view of infrastructure health.

---

## CloudWatch and EC2

CloudWatch can monitor EC2 instances.

Common metrics include:

* CPU utilization
* Network In
* Network Out
* Disk-related metrics depending on monitoring/configuration

Example:

```text
EC2
 |
 ↓
CloudWatch
 |
 ├── CPU
 ├── Network
 └── Other Metrics
```

If an EC2 server is running slowly, CloudWatch can help determine whether resource utilization is contributing to the problem.

---

## CloudWatch and RDS

CloudWatch can monitor RDS databases.

Examples:

* CPU utilization
* Database connections
* Free storage
* Read/write activity
* Network traffic

Example:

```text
RDS
 |
 ↓
CloudWatch
 |
 ├── CPU
 ├── Connections
 └── Storage
```

---

## CloudWatch and Lambda

Lambda automatically integrates with CloudWatch.

CloudWatch can provide:

* Invocation metrics
* Duration
* Errors
* Throttles
* Logs

Example:

```text
Lambda
 |
 ↓
CloudWatch
 |
 ├── Metrics
 └── Logs
```

This is especially useful when troubleshooting failed Lambda functions.

---

## CloudWatch and Sheepeye

CloudWatch can be used to monitor the Sheepeye EC2 server.

A practical setup could be:

```text
Sheepeye EC2
     |
     ↓
CloudWatch
     |
 ├── CPU Metrics
 ├── Application Logs
 └── Alarms
        |
        ↓
       SNS
        |
        ↓
   Notification
```

For example, an alarm could notify you when EC2 CPU utilization remains unusually high.

You can also use CloudWatch Logs for application or system logs where appropriate.

---

## CloudWatch for Troubleshooting

CloudWatch is useful when investigating problems.

### Example: Website is slow

Check:

```text
1. EC2 CPU
2. Memory
3. Network activity
4. Application logs
5. Database metrics
```

### Example: Lambda is failing

Check:

```text
1. Lambda errors
2. Lambda duration
3. CloudWatch Logs
4. IAM permissions
5. Trigger configuration
```

### Example: Database performance problem

Check:

```text
1. RDS CPU
2. Database connections
3. Free storage
4. Read/write activity
5. Application logs
```

---

## CloudWatch vs CloudTrail

These services are different.

### CloudWatch

Primarily used for:

* Monitoring
* Metrics
* Logs
* Alarms
* Operational visibility

### CloudTrail

Primarily used for:

* Recording AWS API activity
* Tracking who performed actions
* Auditing account activity

Example:

```text
CloudWatch → "Is the system healthy?"

CloudTrail  → "Who changed this AWS resource?"
```

Both services are important in AWS environments.

---

## Key Points

* CloudWatch is an AWS **monitoring and observability service**.
* Metrics are numerical measurements.
* Logs contain application or system log data.
* Alarms monitor metrics and can trigger actions.
* SNS can be used to send CloudWatch alarm notifications.
* CloudWatch Dashboards provide a central monitoring view.
* CloudWatch can monitor EC2, RDS, Lambda, and many other AWS services.
* CloudWatch Agent can collect additional operating-system-level metrics.
* EventBridge is commonly used for modern event-driven event routing.
* CloudWatch helps with infrastructure monitoring and troubleshooting.
* **CloudWatch = monitoring and operational visibility.**
* **CloudTrail = AWS API activity and auditing.**

