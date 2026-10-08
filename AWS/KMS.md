# AWS Key Management Service (KMS)

## What is AWS KMS?

AWS Key Management Service (**KMS**) is a managed AWS service used to **create and control encryption keys**.

Encryption protects data by converting readable information into an unreadable form that can only be accessed using the appropriate key.

```text
Plaintext
   |
   ↓
Encryption Key
   |
   ↓
Encrypted Data
```

KMS is commonly used with services such as:

* S3
* EBS
* RDS
* EFS
* Secrets Manager
* CloudTrail
* Lambda

---

## Why Use KMS?

Cloud applications store sensitive information such as:

* Customer data
* Database information
* Files
* Credentials
* Application secrets
* Logs

Encryption helps protect this data if unauthorized access occurs.

Example:

```text
Application
    |
    ↓
Encrypted Data
    |
    ↓
AWS KMS Key
```

KMS provides centralized control over encryption keys and their permissions.

---

## Encryption at Rest vs Encryption in Transit

### Encryption at Rest

Protects data while it is stored.

Examples:

```text
S3
EBS
RDS
EFS
```

### Encryption in Transit

Protects data while it is moving between systems.

Common technologies include:

* HTTPS
* TLS
* SSL/TLS certificates

KMS is primarily associated with **encryption at rest and key management**, although KMS-generated keys can also be used as part of broader application encryption designs.

---

## KMS Keys

A KMS key is used to encrypt or decrypt data, directly or indirectly depending on the service and encryption design.

AWS services commonly use KMS keys to encrypt data encryption keys.

Conceptually:

```text
KMS Key
   |
   ↓
Data Encryption Key
   |
   ↓
Application Data
```

This design allows large amounts of data to be encrypted efficiently.

---

## Symmetric KMS Keys

Symmetric KMS keys use the **same key material for encryption and decryption**.

```text
Encryption
    |
    ↓
Same Key
    |
    ↓
Decryption
```

Symmetric KMS keys are commonly used for AWS service encryption.

For example:

```text
S3
 ↓
KMS
 ↓
Encrypt object
```

---

## Asymmetric KMS Keys

Asymmetric KMS keys use a **public key and a private key**.

```text
Public Key
    ↓
Encryption

Private Key
    ↓
Decryption
```

They can be useful for specific cryptographic use cases where public/private key operations are required.

For typical AWS storage encryption scenarios, symmetric KMS keys are more commonly encountered.

---

## AWS Managed Keys

AWS services can create and manage keys for you.

These are called **AWS managed KMS keys**.

They are useful when you want AWS to handle much of the key management while still using KMS-based encryption.

Example:

```text
AWS Service
    |
    ↓
AWS Managed KMS Key
    |
    ↓
Encrypted Data
```

---

## Customer Managed Keys

A **customer managed key** gives you greater control over the key.

You can manage:

* Key policies
* Permissions
* Rotation configuration
* Key aliases
* Key lifecycle
* Access control

Example:

```text
Your Account
    |
    ↓
Customer Managed KMS Key
    |
    ├── S3
    ├── RDS
    └── EBS
```

Customer managed keys are useful when you need more control over encryption and access.

---

## Key Policy

A KMS key policy controls who can use or manage a KMS key.

Example:

```text
KMS Key
   |
   ↓
Key Policy
   |
 ┌─┴────────────┐
 ↓              ↓
Admin          Application Role
Manage         Use Key
```

KMS permissions involve both IAM and key policies, depending on the access design.

---

## IAM Policy vs KMS Key Policy

This is an important distinction.

### IAM Policy

An IAM policy can grant a principal permission to perform actions such as:

```text
kms:Encrypt
kms:Decrypt
kms:GenerateDataKey
```

### Key Policy

The KMS key policy is the primary resource-based policy mechanism for controlling access to the KMS key.

Conceptually:

```text
IAM Policy
     +
KMS Key Policy
     ↓
Effective KMS Access
```

An explicit deny can still override an allow.

---

## Envelope Encryption

AWS KMS commonly uses **envelope encryption**.

Instead of using the KMS key to directly encrypt a large file, KMS can generate a **data encryption key (DEK)**.

Conceptually:

```text
KMS Key
   |
   ↓
Data Encryption Key
   |
   ↓
Encrypt Large Data
```

The data encryption key encrypts the actual data.

The KMS key protects the data encryption key.

```text
                 KMS Key
                    |
                    ↓
            Encrypt DEK
                    |
                    ↓
             Encrypted DEK

Data ──→ DEK ──→ Encrypted Data
```

This is an important concept behind many AWS encryption services.

---

## KMS and S3

S3 supports server-side encryption using KMS keys.

Conceptually:

```text
User
 |
 ↓
S3
 |
 ↓
KMS
 |
 ↓
Encrypted Object
```

KMS provides control over the encryption key.

You may see this referred to as **SSE-KMS**.

---

## KMS and EBS

EBS volumes can be encrypted using KMS.

```text
EC2
 |
 ↓
Encrypted EBS Volume
 |
 ↓
KMS Key
```

EBS encryption can protect:

* Volume data
* Snapshots
* Data stored on the volume

When creating encrypted EBS resources, AWS handles the encryption process using KMS-backed keys.

---

## KMS and RDS

RDS databases can also use KMS-based encryption at rest.

```text
Application
    |
    ↓
RDS
    |
    ↓
KMS
    |
    ↓
Encrypted Database Storage
```

Encryption can apply to the database storage and certain associated resources.

The exact encryption behavior depends on the RDS service and configuration.

---

## KMS and EFS

EFS supports encryption at rest using KMS.

```text
Application
    |
    ↓
EFS
    |
    ↓
KMS
```

This protects stored file-system data.

---

## KMS and Secrets

Services such as AWS Secrets Manager can use KMS to protect secret data.

Example:

```text
Application
    |
    ↓
Secrets Manager
    |
    ↓
KMS
    |
    ↓
Encrypted Secret
```

This can help protect sensitive values such as database credentials and API keys.

---

## Key Rotation

Key rotation means periodically changing the cryptographic key material used by a key.

Rotation can reduce the risk associated with long-term use of the same cryptographic material.

For supported KMS keys, AWS provides automatic rotation options.

The important concept is:

```text
KMS Key
   |
   ↓
Rotation
   |
   ↓
New Key Material
```

Key rotation does not mean that previously encrypted data suddenly becomes unusable.

---

## Key Alias

An alias is a friendly name for a KMS key.

Example:

```text
alias/sheepeye-production
```

Instead of referring to a key by a long identifier, applications can use a meaningful alias where supported.

Aliases can make key management easier.

---

## Key States

KMS keys can have different states during their lifecycle.

For example:

```text
Enabled
   ↓
Disabled
   ↓
Pending Deletion
   ↓
Deleted
```

A disabled key cannot be used for normal cryptographic operations.

Key deletion should be handled carefully because encrypted data may become inaccessible if the required key is permanently deleted.

---

## KMS Grants

KMS supports **grants** as another way to provide permissions to use a KMS key.

Grants are useful when AWS services or applications need controlled access to a key without changing the key policy every time.

For entry-level AWS work, understand the concept rather than memorizing the detailed grant API.

---

## Cross-Account KMS Access

KMS keys can be used in cross-account scenarios when the appropriate permissions and key policies are configured.

Example:

```text
Account A
   |
   ↓
KMS Key
   ↑
   |
Account B
```

Both the key policy and the relevant IAM permissions must be configured correctly.

---

## KMS vs Secrets Manager

These services are different.

| Service         | Main Purpose               |
| --------------- | -------------------------- |
| KMS             | Manage encryption keys     |
| Secrets Manager | Store and retrieve secrets |

Example:

```text
Secrets Manager
      |
      ↓
Database Password
      |
      ↓
KMS Encryption
```

Secrets Manager stores and manages the secret.

KMS manages the encryption key used to protect the secret.

---

## KMS vs ACM

KMS and AWS Certificate Manager solve different problems.

| Service | Purpose                   |
| ------- | ------------------------- |
| KMS     | Encryption key management |
| ACM     | TLS/SSL certificates      |

Example:

```text
HTTPS
  ↓
ACM Certificate
  ↓
Secure Connection
```

versus:

```text
Data at Rest
  ↓
KMS
  ↓
Encryption
```

---

## KMS and Sheepeye

For your Sheepeye project, KMS can be relevant if you enable encryption using customer-managed keys for services such as:

```text
S3
RDS
EBS
```

For example:

```text
Sheepeye
   |
   ├── EC2
   │    └── Encrypted EBS
   │
   ├── S3
   │    └── SSE-KMS
   │
   └── RDS
        └── KMS Encryption
```

You do **not** need to add complex KMS architecture just to make Sheepeye impressive.

For your portfolio, understanding why encryption is used and how AWS services integrate with KMS is more important.

---

## KMS in SAA Scenarios

KMS is commonly relevant when a question mentions:

* Encryption at rest
* Customer-controlled encryption keys
* Centralized key management
* Key rotation
* S3 encryption
* EBS encryption
* RDS encryption
* Cross-account encryption
* Controlling access to encryption keys

Example:

> A company needs S3 objects encrypted using keys that it controls and wants to manage access to those encryption keys.

A suitable solution is:

```text
S3
 |
 ↓
SSE-KMS
 |
 ↓
Customer Managed KMS Key
```

---

## Important Exam Distinctions

### KMS vs Encryption

KMS is not simply "the encryption."

KMS is primarily a **key management service** that provides cryptographic operations and manages keys.

### KMS vs IAM

```text
IAM
 ↓
Who can access AWS resources

KMS
 ↓
Who can use/manage encryption keys
```

### KMS vs Secrets Manager

```text
KMS
 ↓
Encryption keys

Secrets Manager
 ↓
Secrets
```

### KMS vs ACM

```text
KMS
 ↓
Encryption/key management

ACM
 ↓
TLS/SSL certificates
```

---

## Key Points

* AWS KMS manages encryption keys.
* KMS is heavily used for encryption at rest.
* Symmetric KMS keys are common for AWS service encryption.
* Customer managed keys provide greater control than AWS managed keys.
* KMS key policies control access to KMS keys.
* IAM policies can also participate in KMS authorization.
* **SCPs, IAM policies, and KMS key policies are different layers of access control.**
* Envelope encryption uses a data encryption key to encrypt data while KMS protects the data key.
* S3, EBS, RDS, EFS and other AWS services can integrate with KMS.
* Key rotation helps manage cryptographic key material over time.
* Deleting a KMS key can make encrypted data inaccessible.
* KMS manages encryption keys; Secrets Manager manages secrets; ACM manages TLS/SSL certificates.
* For SAA, focus on understanding **when KMS is needed and how it integrates with AWS services**, rather than memorizing cryptographic details.
