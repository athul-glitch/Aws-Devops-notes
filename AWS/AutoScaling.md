# EC2 Auto Scaling

## What is EC2 Auto Scaling?

Amazon EC2 Auto Scaling automatically adjusts the number of EC2 instances running in an application based on demand.

It can:

* Launch new EC2 instances
* Terminate unnecessary instances
* Maintain a desired number of instances
* Replace unhealthy instances
* Scale based on demand

Basic architecture:

```text
Users
  |
  ↓
Load Balancer
  |
  ↓
Auto Scaling Group
  |
 ┌┴─────────┐
 ↓          ↓
EC2        EC2
```

---

## Why Use Auto Scaling?

Without Auto Scaling:

```text
Users
  |
  ↓
Single EC2
```

If traffic suddenly increases, the server may become overloaded.

With Auto Scaling:

```text
Low traffic
    ↓
2 EC2 instances

High traffic
    ↓
4 EC2 instances
```

The number of instances can increase or decrease according to the configured scaling policy.

---

## Auto Scaling Group

An **Auto Scaling Group (ASG)** manages a group of EC2 instances.

The ASG defines how many instances should normally be running.

Important settings include:

* Minimum capacity
* Desired capacity
* Maximum capacity
* Availability Zones
* Launch template
* Health checks
* Scaling policies

Example:

```text
Minimum     = 2
Desired     = 2
Maximum     = 5
```

The ASG tries to maintain the desired capacity while respecting the minimum and maximum limits.

---

## Minimum, Desired and Maximum Capacity

### Minimum Capacity

The minimum number of instances the ASG should maintain.

```text
Minimum = 2
```

Normally, the ASG will not intentionally scale below this value.

### Desired Capacity

The preferred number of instances.

```text
Desired = 2
```

### Maximum Capacity

The maximum number of instances that can be launched by the ASG.

```text
Maximum = 5
```

Example:

```text
ASG
 |
 ├── Minimum = 2
 ├── Desired = 2
 └── Maximum = 5
```

---

## Launch Template

A Launch Template defines how new EC2 instances should be created.

It can specify:

* AMI
* Instance type
* Key pair
* Security Groups
* IAM role
* User data
* Storage
* Network configuration

Example:

```text
Launch Template
      |
      ↓
Auto Scaling Group
      |
 ┌────┼────┐
 ↓    ↓    ↓
EC2  EC2  EC2
```

When Auto Scaling needs another instance, it uses the launch template.

---

## Auto Scaling Across Availability Zones

An ASG can distribute instances across multiple Availability Zones.

Example:

```text
AWS Region
   |
 ┌─┴──────────────┐
 ↓                ↓
AZ A              AZ B
 |                 |
EC2               EC2
```

This improves availability because the application is not dependent on a single Availability Zone.

---

## Health Checks

Auto Scaling can use health checks to determine whether an instance is healthy.

If an instance becomes unhealthy, the ASG can terminate it and launch a replacement.

Example:

```text
ASG
 |
 ├── EC2-1 → Healthy
 ├── EC2-2 → Unhealthy
 |
 ↓
Terminate EC2-2
 |
 ↓
Launch replacement
```

This helps maintain the desired capacity.

---

## Auto Scaling with ALB

Auto Scaling is commonly used with an Application Load Balancer.

```text
Users
  |
  ↓
ALB
  |
  ↓
Target Group
  |
 ┌┴─────────┐
 ↓          ↓
EC2        EC2
 ↑          ↑
 └── Auto Scaling ──┘
```

The ALB distributes traffic.

The ASG manages the number of EC2 instances.

---

## Scaling Policies

Scaling policies determine when the ASG should add or remove instances.

### Target Tracking

Target tracking tries to maintain a target metric value.

Example:

```text
Target CPU = 50%
```

If average CPU usage becomes too high, Auto Scaling can add instances.

When demand decreases, it can remove instances.

---

### Step Scaling

Step scaling changes capacity by different amounts depending on how far a metric moves beyond a threshold.

Example:

```text
CPU < 50%       → No change
CPU 50–70%      → Add 1 instance
CPU 70–90%      → Add 2 instances
CPU > 90%       → Add 3 instances
```

---

### Scheduled Scaling

Scheduled scaling changes capacity at a known time.

Example:

```text
10:00 AM → Increase instances
10:00 PM → Reduce instances
```

Useful when traffic follows predictable patterns.

---

## Scale Out vs Scale In

### Scale Out

Increase the number of instances.

```text
2 EC2
 ↓
4 EC2
```

Used when demand increases.

### Scale In

Decrease the number of instances.

```text
4 EC2
 ↓
2 EC2
```

Used when demand decreases.

---

## Auto Scaling and CloudWatch

Auto Scaling can use CloudWatch metrics for scaling decisions.

Example:

```text
EC2
 |
 ↓
CloudWatch Metric
 |
 ↓
Scaling Policy
 |
 ↓
Auto Scaling Group
 |
 ├── Launch EC2
 └── Terminate EC2
```

For example, sustained high CPU utilization can trigger a scale-out action.

---

## Auto Scaling and SNS

Auto Scaling can integrate with notification mechanisms for certain events.

For example, notifications can be used to monitor scaling activity.

```text
Auto Scaling Event
       |
       ↓
Notification
       |
       ↓
Administrator
```

---

## Auto Scaling and Cost

Auto Scaling can reduce unnecessary compute usage.

During low demand:

```text
5 EC2
 ↓
2 EC2
```

During high demand:

```text
2 EC2
 ↓
5 EC2
```

This allows capacity to better match demand.

However, scaling does not automatically guarantee lower costs. Poorly configured scaling policies or minimum capacity can still result in unnecessary resource usage.

---

## Auto Scaling and Sheepeye

A future highly available Sheepeye architecture could be:

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
 ↓
Auto Scaling Group
 |
 ┌───────────────┐
 ↓               ↓
EC2             EC2
```

The ASG could maintain a minimum number of instances and add more instances during periods of higher traffic.

However, you **do not need Auto Scaling immediately** for your current Sheepeye project.

Your current single-EC2 setup is sufficient for learning and demonstrating basic AWS infrastructure.

---

## Auto Scaling vs Load Balancing

These services have different responsibilities.

| Service       | Main Responsibility             |
| ------------- | ------------------------------- |
| Auto Scaling  | Adjusts number of EC2 instances |
| Load Balancer | Distributes traffic             |
| CloudWatch    | Provides metrics and monitoring |

They often work together:

```text
Users
  |
  ↓
ALB
  |
  ↓
EC2 Instances
  ↑
  |
Auto Scaling
  ↑
  |
CloudWatch Metrics
```

---

## Key Points

* EC2 Auto Scaling automatically adjusts the number of EC2 instances.
* An Auto Scaling Group manages the instances.
* Minimum, desired, and maximum capacity control the group size.
* A Launch Template defines how new EC2 instances are created.
* Auto Scaling can distribute instances across Availability Zones.
* Unhealthy instances can be replaced automatically.
* **Scale out** means adding instances.
* **Scale in** means removing instances.
* Target tracking, step scaling, and scheduled scaling are common scaling approaches.
* CloudWatch metrics can be used to trigger scaling.
* Auto Scaling is commonly combined with an ALB.
* **ALB distributes traffic; ASG manages capacity.**
* Auto Scaling improves availability and helps match capacity to demand.
* You do not need Auto Scaling in every small application; use it when the workload and availability requirements justify it.

