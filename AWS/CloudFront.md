# Amazon CloudFront

## What is Amazon CloudFront?

Amazon CloudFront is an AWS **Content Delivery Network (CDN)**.

It delivers content to users from locations closer to them, reducing latency and improving application performance.

CloudFront can deliver:

* Web pages
* Images
* Videos
* JavaScript
* CSS
* API responses
* Static files

Basic flow:

```text
User
 |
 ↓
CloudFront Edge Location
 |
 ↓
Origin
```

---

## What is a CDN?

A CDN stores or caches content at locations distributed around the world.

Instead of every user accessing the original server directly:

```text
User
  |
  ↓
Origin Server
```

a CDN can serve cached content from a nearby location:

```text
User
  |
  ↓
Nearby CloudFront Edge Location
  |
  ↓
Cached Content
```

This can reduce latency and decrease the number of requests reaching the origin.

---

## CloudFront Edge Locations

CloudFront uses a globally distributed network of edge locations.

When a user requests content, CloudFront attempts to serve it from an appropriate nearby edge location.

Example:

```text
User in India
      |
      ↓
CloudFront Edge Location
      |
      ↓
Origin
```

The exact edge location used is determined by CloudFront.

---

## Origin

An origin is the location from which CloudFront retrieves content when it is not already available in the cache.

CloudFront supports origins such as:

* Amazon S3
* Application Load Balancer
* EC2
* API Gateway
* Other HTTP/HTTPS servers

Example:

```text
CloudFront
    |
    ↓
ALB
    |
    ↓
EC2
```

---

## CloudFront with S3

A common architecture is:

```text
User
 |
 ↓
CloudFront
 |
 ↓
S3 Bucket
```

This is useful for static websites and static assets.

Examples:

* HTML
* CSS
* JavaScript
* Images
* Videos

CloudFront caches frequently requested objects at edge locations.

---

## CloudFront with EC2

CloudFront can also sit in front of an EC2-hosted application.

```text
User
 |
 ↓
CloudFront
 |
 ↓
EC2
 |
 ↓
Application
```

This can improve delivery of cacheable content and provide additional security and performance features.

---

## CloudFront with Load Balancer

A common highly available architecture is:

```text
Users
  |
  ↓
CloudFront
  |
  ↓
Application Load Balancer
  |
 ┌┴──────────┐
 ↓           ↓
EC2         EC2
```

The ALB distributes traffic between application servers.

CloudFront provides CDN capabilities in front of the application.

---

## CloudFront Caching

CloudFront can cache objects at edge locations.

Example:

```text
First Request
User → CloudFront → Origin
                    |
                    ↓
                 Content
                    |
                    ↓
              CloudFront Cache


Later Request
User → CloudFront → Cached Content
```

The second request may be served directly from the CloudFront cache.

This reduces the number of requests reaching the origin.

---

## Cache Hit and Cache Miss

### Cache Hit

The requested object is already available in the CloudFront cache.

```text
User
 |
 ↓
CloudFront
 |
 ↓
Cached Object
```

The origin does not need to provide the object again.

### Cache Miss

The object is not currently available in the cache.

```text
User
 |
 ↓
CloudFront
 |
 ↓
Origin
 |
 ↓
Object
```

CloudFront retrieves the object from the origin and can cache it.

---

## TTL

CloudFront uses caching policies to determine how long objects can remain cached.

TTL stands for **Time to Live**.

A longer TTL can reduce requests to the origin, while a shorter TTL allows content changes to become visible more quickly.

Caching should be configured according to the application requirements.

---

## Cache Invalidation

Sometimes you need CloudFront to remove cached content before the normal TTL expires.

For example:

```text
Old website file
       |
       ↓
CloudFront Cache
```

After deploying a new version, you may need to invalidate the old cached object.

Example:

```text
CloudFront Invalidation
        |
        ↓
Remove cached object
        |
        ↓
Next request gets updated content
```

Invalidation is useful when content must be refreshed before the cache naturally expires.

---

## CloudFront and HTTPS

CloudFront supports HTTPS connections.

AWS Certificate Manager (ACM) can be used to provide an SSL/TLS certificate for a CloudFront distribution.

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

For CloudFront distributions, certificates are associated with ACM in the **US East (N. Virginia) region (`us-east-1`)**.

---

## CloudFront and Route 53

Route 53 can route a domain to a CloudFront distribution.

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
CloudFront
 |
 ↓
Origin
```

A Route 53 Alias record can be used to point the domain to CloudFront.

---

## CloudFront Security

CloudFront can help protect applications using features such as:

* HTTPS
* AWS WAF integration
* Origin access controls for S3
* Geographic restrictions
* Signed URLs
* Signed cookies

For example:

```text
User
 |
 ↓
CloudFront
 |
 ↓
AWS WAF
 |
 ↓
Origin
```

AWS WAF can inspect web requests and block unwanted traffic.

---

## CloudFront with Private S3 Content

S3 content does not always need to be publicly accessible.

A more secure architecture is:

```text
User
 |
 ↓
CloudFront
 |
 ↓
Private S3 Bucket
```

CloudFront can be configured to access the S3 bucket while the bucket itself remains private.

This avoids making the S3 bucket publicly accessible just to serve website content.

---

## CloudFront Distributions

A CloudFront distribution defines how CloudFront delivers content.

Configuration can include:

* Origin
* Cache behavior
* Allowed HTTP methods
* HTTPS settings
* Viewer protocol policy
* Cache policy
* Origin request policy
* Geographic restrictions

Example:

```text
CloudFront Distribution
        |
   ┌────┴────┐
   ↓         ↓
 Origin   Cache Rules
```

---

## CloudFront and APIs

CloudFront can also be placed in front of APIs.

Example:

```text
User
 |
 ↓
CloudFront
 |
 ↓
API Gateway
 |
 ↓
Lambda
 |
 ↓
Database
```

This can provide caching, HTTPS, and edge delivery capabilities depending on the API architecture.

---

## CloudFront and Sheepeye

A possible future Sheepeye architecture is:

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
Application Load Balancer
 |
 ↓
EC2
 |
 ↓
Sheepeye Application
```

CloudFront could also be used to serve static assets such as:

```text
Images
CSS
JavaScript
```

However, there is no need to add CloudFront immediately if the current EC2 setup already works and AWS cost is a concern.

---

## CloudFront vs Route 53

These services solve different problems.

| Service    | Main Purpose                   |
| ---------- | ------------------------------ |
| Route 53   | DNS and traffic routing        |
| CloudFront | CDN, caching, content delivery |

They are commonly used together:

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

## CloudFront vs S3

S3 stores objects.

CloudFront distributes and caches content.

```text
S3
 ↓
Storage

CloudFront
 ↓
Content Delivery
```

They can be combined:

```text
User
 |
 ↓
CloudFront
 |
 ↓
S3
```

---

## Key Points

* CloudFront is an AWS **Content Delivery Network (CDN)**.
* It uses globally distributed edge locations.
* CloudFront can cache content closer to users.
* An origin is the source from which CloudFront retrieves content.
* Common origins include S3, EC2, ALB, and API Gateway.
* A **cache hit** means the requested object is already cached.
* A **cache miss** causes CloudFront to retrieve the object from the origin.
* TTL controls how long content can remain cached.
* Cache invalidation can remove cached objects before their normal expiration.
* CloudFront supports HTTPS and integrates with ACM.
* CloudFront can integrate with AWS WAF for web application protection.
* Route 53 can point a domain to CloudFront using an Alias record.
* CloudFront can work with private S3 buckets.
* CloudFront improves content delivery but does not replace the origin.
* **Route 53 = DNS.**
* **CloudFront = CDN and caching.**
* **S3 = object storage.**
