# Key Management Service (KMS)

* Regional & Public Service
* Create, Store and Manage Keys
* Symmetric and Asymmetric Keys
* Cryptographic operations (encrypt, decrypt & ...)
* **IMPORTANT:** Keys never leave KMS - Provides FIPS 140-2 (L2)

## KMS Keys

* KMS Keys are logical - ID, date, policy, desc & state
* ..... backed by **physical** key material
* **Generated or Imported**
* **IMPORTANT:** KMS Keys can be used for up to 4KB of data

## Data Encryption Keys (DEKs)

* **GenerateDataKey - works on > 4KB**
* **Plaintext Version**
* **Ciphertext Version**
* **Encrypt** data using **plaintext key**
* **Discard** (plaintext key)
* **Store encrypted key** with data

## Key Concepts

* KMS Keys are isolated to a **region** & never leave..
* ... Multi-region keys discussed (if required) in a different video
* ... AWS Owned & Customer Owned
* ... w/ Customer owned...**AWS Managed** or **Customer Managed KEYS**
* Customer managed keys are more configurable
* KMS Keys support rotation
* **Backing Key** (and **previous** backing keys)
* **IMPORTANT:** Aliases

## Key Policies and Security

* Key Policies (Resource)
* Every KEY has one!
* Key Policies + IAM Policies
* Key Policies + Grants

### Example Key Policy

```json
{
  "Sid": "Enable IAM User Permissions",
  "Effect": "Allow",
  "Principal": {"AWS": "arn:aws:iam::111122223333:root"},
  "Action": "kms:*",
  "Resource": "*"
}
```

### Example IAM Policy

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": [
      "kms:Encrypt",
      "kms:Decrypt"
    ],
    "Resource": [
      "arn:aws:kms:*:111122223333:key/*"
    ]
  }
}
```

## S3 Bucket Keys

* CloudTrail KMS events now show the **bucket**..
* ... **Not the Object**
* Works with **replication** .. the object encryption is maintained
* **IMPORTANT:** .. if replicating plaintext to a bucket using bucket keys the object is encrypted at the destination side (**ETAG changes**)

## S3 Object Storage Classes
### S3 Storage Classes - S3 Standard

#### Key Features

* **Milliseconds** first byte latency
* Objects stored in S3 Standard **can be made publicly available**
* API provides **HTTP/1.1 200 OK response**

#### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** Use S3 Standard for **Frequently Accessed Data which is important and Non Replaceable**

#### Billing Model

* **GB/m fee** for data stored
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **No specific retrieval fee, no minimum duration, no minimum size**

### S3 Storage Classes - S3 Standard-IA

#### Key Features

* **Milliseconds** first byte latency
* API provides **HTTP/1.1 200 OK response**

#### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 Standard-IA should be used for **long-lived data, which is important but where access is infrequent**

#### Billing Model

* **GB/m fee** for data stored
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **IMPORTANT:** S3 Standard-IA has a **per GB data retrieval fee**, overall cost increases with frequent data access
* **IMPORTANT:** Standard-IA has a **minimum duration charge of 30 days** - objects can be stored for less, but the minimum billing always applies
* **IMPORTANT:** Standard-IA has a **minimum capacity charge of 128KB per object**

### S3 Storage Classes - S3 One Zone-IA

#### Key Features

* **Milliseconds** first byte latency
* API provides **HTTP/1.1 200 OK response**

#### Durability and Replication

* **99.5% durability** (lower than Standard-IA due to single AZ)
* **Replication in 1 AZ** (Availability Zone)
* Objects are stored in **a single AZ** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 One Zone-IA should be used for **long-lived data, which is NON-CRITICAL & REPLACEABLE and where access is infrequent**

#### Billing Model

* **GB/m fee** for data stored (lower cost than Standard-IA)
* **IMPORTANT:** S3 One Zone-IA has a **per GB data retrieval fee**, overall cost increases with frequent data access
* **IMPORTANT:** One Zone-IA has a **minimum duration charge of 30 days** - objects can be stored for less, but the minimum billing always applies
* **IMPORTANT:** One Zone-IA has a **minimum capacity charge of 128KB per object**

### S3 Storage Classes - S3 Glacier - Instant Retrieval

#### Key Features

* **Milliseconds** first byte latency (instant retrieval)
* API provides **HTTP/1.1 200 OK response**

#### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 Glacier Instant Retrieval should be used for **archive data that needs immediate access, accessed once per quarter**

#### Billing Model

* **GB/m fee** for data stored (lower cost than Standard-IA)
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **IMPORTANT:** S3 Glacier Instant Retrieval has a **per GB data retrieval fee**
* **IMPORTANT:** Glacier Instant Retrieval has a **minimum duration charge of 90 days** - objects can be stored for less, but the minimum billing always applies
* **IMPORTANT:** Glacier Instant Retrieval has a **minimum capacity charge of 128KB per object**

### S3 Storage Classes - S3 Glacier - Flexible Retrieval

#### Key Features

* **Retrieval options:** Expedited (1-5 minutes), Standard (3-5 hours), Bulk (5-12 hours)
* API provides **HTTP/1.1 200 OK response** (after retrieval initiation)

### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 Glacier Flexible Retrieval should be used for **archive data that is rarely accessed, accessed once or twice per year**

#### Billing Model

* **GB/m fee** for data stored (lower cost than Instant Retrieval)
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **IMPORTANT:** S3 Glacier Flexible Retrieval has a **per GB data retrieval fee** (varies by retrieval option)
* **IMPORTANT:** Glacier Flexible Retrieval has a **minimum duration charge of 90 days** - objects can be stored for less, but the minimum billing always applies
* **IMPORTANT:** Glacier Flexible Retrieval has a **minimum capacity charge of 40KB per object**

### S3 Storage Classes - S3 Glacier Deep Archive

#### Key Features

* **Retrieval options:** Standard (12 hours), Bulk (48 hours)
* Lowest cost storage class
* API provides **HTTP/1.1 200 OK response** (after retrieval initiation)

#### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 Glacier Deep Archive should be used for **archive data that is rarely accessed, accessed once per year or less, long-term retention**

#### Billing Model

* **GB/m fee** for data stored (lowest cost storage class)
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **IMPORTANT:** S3 Glacier Deep Archive has a **per GB data retrieval fee** (varies by retrieval option)
* **IMPORTANT:** Glacier Deep Archive has a **minimum duration charge of 180 days** - objects can be stored for less, but the minimum billing always applies
* **IMPORTANT:** Glacier Deep Archive has a **minimum capacity charge of 40KB per object**

### S3 Storage Classes - S3 Intelligent-Tiering

#### Key Features

* **Milliseconds** first byte latency
* **Automatic cost optimization** - moves objects between access tiers automatically
* **No retrieval fees** or minimum storage duration
* API provides **HTTP/1.1 200 OK response**

#### Durability and Replication

* **99.999999999% durability** for 10,000,000 objects
* **1 object loss per 10,000 years**
* **Replication over 3 AZs** (Availability Zones)
* Objects are replicated across **at least 3 AZs** in the AWS region
* **Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)** are used to detect and fix any data corruption

#### Use Case

* **IMPORTANT:** S3 Intelligent-Tiering should be used for **data with unknown or changing access patterns, automatically optimizes costs**

#### Billing Model

* **GB/m fee** for data stored (varies by tier)
* **$ per GB charge for transfer OUT** (IN is free)
* **Price per 1,000 requests**
* **IMPORTANT:** S3 Intelligent-Tiering has a **small monthly monitoring and automation fee per object** (to track access patterns)
* **No specific retrieval fee, no minimum duration, no minimum size**
* Automatically moves objects between tiers: Frequent Access, Infrequent Access, Archive Instant Access, Archive Access, Deep Archive Access

## S3 Lifecycle Configuration

### Overview

* A **lifecycle configuration** is a set of rules that define actions to be applied to a bucket or groups of objects
* Rules consist of **actions** that can be applied to objects based on their age or other criteria
* Lifecycle rules can be applied to:
  * Entire bucket
  * Groups of objects (using prefixes or tags)
* **IMPORTANT:** Lifecycle rules are particularly useful when S3 buckets have **versioning enabled**, as they can help manage multiple versions of objects

### Types of Lifecycle Actions

#### Transition Actions
* **Move objects** between storage classes automatically based on age
* Example: Move objects from S3 Standard to S3 Standard-IA after 30 days
* Example: Move objects from S3 Standard-IA to S3 Glacier after 90 days
* Helps optimize costs by automatically moving data to cheaper storage classes as access patterns change

#### Expiration Actions
* **Automatically delete** objects after a specified number of days
* Useful for temporary data, logs, or data that has reached its retention period
* Can be configured to delete current versions and/or previous versions (when versioning is enabled)

### S3 Lifecycle Configuration - Transitions

![S3 Lifecycle Diagram](S3lifecycleconfigtransitions.png)

* Lifecycle transitions allow automatic movement of objects between storage classes
* Common transition path: **S3 Standard → S3 Standard-IA → S3 Glacier → S3 Glacier Deep Archive**
* Transitions are based on object age (days since creation)
* **IMPORTANT:** Each transition action can have different age thresholds
* **IMPORTANT:** Transitions help reduce storage costs while maintaining data availability

## S3 Replication

### Overview

S3 Replication automatically replicates objects from a source bucket to a destination bucket. There are two main types:

#### Cross-Region Replication (CRR)
* Replicates objects to a **different AWS region**
* Use cases: Compliance requirements, disaster recovery, global content distribution

#### Same-Region Replication (SRR)
* Replicates objects within the **same AWS region**
* Use cases: Log aggregation, production and test environment synchronization, maintaining multiple copies for compliance

### Important Considerations

* **IMPORTANT:** By default, replication is **NOT retroactive** - only new objects uploaded after replication is configured will be replicated
* **IMPORTANT:** **Versioning must be ON** on both source and destination buckets for replication to work
* **Batch replication** can be used to replicate existing objects that were created before replication was configured
* **Replication direction:**
  * **One-way replication** (source to destination) is the default
  * **Bi-directional replication** is possible but requires separate replication rules in each direction
* **Encryption support:**
  * **Unencrypted** objects can be replicated
  * **SSE-S3** encrypted objects can be replicated
  * **SSE-KMS** encrypted objects can be replicated (requires additional configuration for KMS key permissions)
  * **SSE-C** (Customer-provided keys) cannot be replicated
* **IMPORTANT:** The **source bucket owner needs permission** to read the objects being replicated
* **Limitations:**
  * **No system events** are replicated
  * Objects in **Glacier** or **Glacier Deep Archive** storage classes are not replicated
  * **Deletes are not replicated by default** (but can be enabled with **DeleteMarkerReplication**)

### Why Use Replication?

#### Same-Region Replication (SRR) Use Cases:
* **Log aggregation** - Centralize logs from multiple buckets into a single destination
* **PROD & TEST sync** - Keep production and test environments synchronized
* **Resilience with strict sovereignty** - Maintain multiple copies within the same region for compliance

#### Cross-Region Replication (CRR) Use Cases:
* **Global resilience improvements** - Protect against regional failures
* **Latency reduction** - Place data closer to end users in different regions

### Amazon S3 Replication Time Control (S3 RTC)

#### Overview
* **S3 Replication Time Control** is a feature that provides **predictable replication time** backed by a **Service Level Agreement (SLA)**
* Helps customers meet compliance or business requirements for data replication
* Provides visibility into the replication process with **Amazon CloudWatch Metrics**

#### Key Features
* **Designed to replicate 99.99% of objects within 15 minutes** after upload
* **Majority of new objects replicated in seconds**
* **SLA commitment:** Replicates 99.9% of objects within 15 minutes during any billing month
* **CloudWatch Metrics** allow monitoring of:
  * Time it takes to complete replication
  * Total number of objects pending replication
  * Total size of objects pending replication

#### When to Use
* When you need **guaranteed replication time** for compliance or business requirements
* When you need **visibility and monitoring** of replication performance
* For critical data that must be replicated quickly and reliably

## S3 PreSigned URLs

### Overview

* **S3 PreSigned URLs** can be used to grant temporary access to S3 objects using a URL
* The URL contains authentication information embedded in the query string
* Allows users to access objects without requiring AWS credentials

### Key Concepts

* **IMPORTANT:** You can create a URL for an object **you have no access to** - the URL will work based on the permissions of the identity that generated it
* **IMPORTANT:** When using the URL, the **permissions match the identity which generated it**
  * If you generate a PreSigned URL, the person using it will have the same permissions you have
* **Access denied errors** could mean:
  * The generating identity never had access to the object
  * The generating identity no longer has access to the object
  * The PreSigned URL has expired
* **IMPORTANT:** **Don't generate PreSigned URLs with a role** - URLs stop working when temporary credentials expire
  * Use IAM users or long-term credentials instead of temporary role credentials
* **Expiration:** PreSigned URLs have a configurable expiration time (default is 1 hour, maximum is 7 days for IAM users, 1 hour for temporary credentials)

### Use Cases

* Allow users to upload files directly to S3 without exposing AWS credentials
* Share private objects temporarily without making them publicly accessible
* Enable direct downloads from S3 without proxying through your application

## S3 Select and Glacier Select

### Overview

* **S3 Select** and **Glacier Select** allow you to retrieve only a subset of data from objects using SQL-like statements
* Instead of retrieving entire objects, you can query and filter data server-side
* Reduces the amount of data transferred and improves query performance

### The Problem They Solve

* **S3 can store huge objects** (up to 5TB per object)
* You often want to retrieve only **part of the object**, not the entire file
* **Retrieving a 5TB object** takes significant time and uses 5TB of bandwidth
* **Filtering at the client side** doesn't reduce the data transfer - you still need to download the entire object first
* This is inefficient and costly for large objects

### How S3/Glacier Select Works

* **S3/Glacier Select** lets you use **SQL-like statements** to query objects
* You can **select part of the object** that matches your query criteria
* **Pre-filtering happens at S3** - only the matching data is returned to you
* This dramatically reduces:
  * **Data transfer** (only matching rows/records are downloaded)
  * **Query time** (filtering happens server-side)
  * **Costs** (you pay for less data transfer)

### Supported Formats

* **CSV** (Comma-Separated Values)
* **JSON** (JavaScript Object Notation)
* **Parquet** (Columnar storage format)
* **Compression:** **Bzip2 compression** is supported for CSV and JSON files

### Use Cases

* Query large log files stored in S3 without downloading entire files
* Extract specific columns from large CSV files
* Filter JSON data based on specific criteria
* Analyze data stored in Parquet format efficiently
* Reduce costs when working with large datasets

## S3 Event Notifications

* **Notification** generated when **events** occur in a **bucket**
* Can be delivered to **SNS**, **SQS** and **Lambda Functions**
* Object **Created** (Put, Post, Copy, CompleteMultiPartUpload)
* Object **Delete** (*, Delete, DeleteMarkerCreated)
* Object **Restore** (Post (Initiated), Completed)
* **Replication** (OperationMissedThreshold, OperationReplicatedAfterThreshold, OperationNotTracked, OperationFailedReplication)

## S3 Object Lock

### Overview

* **S3 Object Lock** is an Amazon S3 capability that helps prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely
* Provides **Write-Once-Read-Many (WORM)** functionality
* **IMPORTANT:** Object Lock can be enabled on **new buckets** only
  * For existing buckets, enabling Object Lock requires a **support request** to AWS
* **IMPORTANT:** Requires **versioning** to be enabled - individual versions are locked, not just the object itself
* A bucket can have **default Object Lock settings** that automatically apply to all new objects uploaded to that bucket

### Key Characteristics

* **Write-Once-Read-Many (WORM)** - **No Delete, No Overwrite** during the lock period
* Once an object version is locked, it cannot be deleted or overwritten until the lock is removed or expires
* Provides protection against:
  * Accidental deletion
  * Malicious deletion
  * Unauthorized modifications
  * Compliance violations

### Lock Mechanisms

S3 Object Lock provides two mechanisms for protecting objects:

1. **Retention Period** - Time-based protection (days, months, or years)
2. **Legal Hold** - Indefinite protection until explicitly removed

* **Both, one, or the other, or none** - You can apply:
  * Both a Retention Period and a Legal Hold
  * Just a Retention Period
  * Just a Legal Hold
  * Neither (no lock)

## S3 Object Lock - Retention

### Overview

* **Retention Period** allows you to specify a fixed amount of time (in **DAYS & YEARS**) during which an object version cannot be deleted or overwritten
* Provides time-based immutability for compliance and data protection requirements

### COMPLIANCE Mode

#### Characteristics

* **COMPLIANCE mode** is the strictest retention mode
* Objects in COMPLIANCE mode **cannot be adjusted, deleted, or overwritten**
* **IMPORTANT:** Even the **account root user** cannot delete or modify objects in COMPLIANCE mode
* Protection remains in effect **until the retention period expires**
* Once the retention period expires, the object can be deleted or modified

#### Use Cases

* Regulatory compliance requirements (SEC Rule 17a-4, FINRA, etc.)
* Legal hold requirements with fixed timeframes
* Audit trail preservation
* When you need absolute immutability that cannot be bypassed

#### Important Notes

* **Retention period cannot be shortened** in COMPLIANCE mode
* **COMPLIANCE mode cannot be changed** to GOVERNANCE mode
* No bypass mechanism exists - even with special permissions, objects cannot be modified during the retention period

### GOVERNANCE Mode

#### Characteristics

* **GOVERNANCE mode** provides more flexible retention
* Objects in GOVERNANCE mode are protected, but **special permissions can be granted** allowing lock settings to be adjusted
* Allows authorized users to modify or delete objects during the retention period if they have the appropriate permissions

#### Bypass Mechanism

* To modify or delete objects in GOVERNANCE mode during the retention period, you need:
  * **IAM Permission:** `s3:BypassGovernanceRetention`
  * **HTTP Header:** `x-amz-bypass-governance-retention:true` (console sets this by default)
* With these permissions, users can:
  * Delete the object version
  * Overwrite the object version
  * Adjust the retention period
  * Change from GOVERNANCE to COMPLIANCE mode

#### Use Cases

* When you need protection but also require flexibility for authorized users
* Development and testing environments
* When compliance requirements allow for authorized overrides
* Scenarios where retention periods might need adjustment

### Comparison: COMPLIANCE vs GOVERNANCE

| Feature | COMPLIANCE Mode | GOVERNANCE Mode |
|---------|----------------|-----------------|
| **Immutability** | Absolute - cannot be bypassed | Can be bypassed with permissions |
| **Root User** | Cannot delete/modify | Cannot delete/modify without bypass permission |
| **Bypass Permission** | None - impossible | `s3:BypassGovernanceRetention` required |
| **Retention Adjustment** | Cannot be shortened | Can be adjusted with permissions |
| **Use Case** | Strict compliance requirements | Flexible protection with authorized overrides |

## S3 Object Lock - Legal Hold

### Overview

* **Legal Hold** is set on an **object version** and can be turned **ON** or **OFF**
* Unlike Retention Period, Legal Hold has **no retention duration** - it's indefinite until explicitly removed
* Provides protection against deletion or changes until the legal hold is removed

### Key Characteristics

* **Set on an object version** - Applied to individual versions, not the entire object
* **ON or OFF** - Binary state that can be toggled
* **No retention period** - Unlike Retention Period, Legal Hold doesn't specify a fixed duration
* **NO Deletes or Changes** until removed - Objects with Legal Hold cannot be deleted or modified until the hold is explicitly removed
* **IMPORTANT:** Requires **`s3:PutObjectLegalHold`** permission to add or remove a legal hold

### How Legal Hold Works

1. **Applying Legal Hold:**
   * Use `s3:PutObjectLegalHold` with `Status=ON`
   * Object version becomes locked and cannot be deleted or updated

2. **During Legal Hold:**
   * Object version is protected from deletion and modification
   * Normal permissions do not allow deletion or updates
   * Protection continues indefinitely until legal hold is removed

3. **Removing Legal Hold:**
   * Use `s3:PutObjectLegalHold` with `Status=OFF`
   * Once removed, normal permissions apply
   * Object can then be deleted or modified based on standard permissions

### Use Cases

* **Prevent accidental deletion of critical object versions**
* Legal and regulatory investigations
* Litigation holds
* Compliance requirements that don't have fixed timeframes
* Protecting evidence or audit trails
* When you need indefinite protection that can be removed when no longer needed

### Legal Hold vs Retention Period

| Feature | Legal Hold | Retention Period |
|---------|------------|------------------|
| **Duration** | Indefinite (until removed) | Fixed (days/years) |
| **Removal** | Manual removal required | Automatic after expiration |
| **Mode** | Single mode | COMPLIANCE or GOVERNANCE |
| **Flexibility** | Can be removed anytime (with permission) | COMPLIANCE: cannot be shortened; GOVERNANCE: can be adjusted with permissions |
| **Use Case** | Indefinite protection needs | Time-based compliance requirements |

## S3 Access Points

### Overview

* **S3 Access Points** simplify managing access to S3 buckets and objects
* Instead of managing a single bucket with one bucket policy, you can create **many access points**, each with different policies and network access controls
* Each access point has its own **endpoint address**, making it easier to route requests to specific access configurations

### The Problem They Solve

* **Traditional approach:** One bucket with one bucket policy
  * As access requirements become more complex, managing a single bucket policy becomes difficult
  * Different applications, teams, or use cases may need different access patterns
  * Network restrictions may need to vary for different access scenarios

* **S3 Access Points solution:**
  * Create multiple access points for the same bucket
  * Each access point can have its own policy
  * Each access point can have different network access controls
  * Simplifies access management and provides better isolation

### Key Features

* **Simplify managing access** to S3 buckets and objects
* **Create many access points** for a single bucket
* **Each with different policies** - Granular access control per access point
* **Each with different network access controls** - VPC-only access, public access, or specific IP ranges
* **Each access point has its own endpoint address** - Unique URL for each access point

### Creating S3 Access Points

#### Via AWS Console
* Navigate to S3 → Access Points
* Create new access point
* Configure bucket, name, policy, and network settings

#### Via AWS CLI

```bash
aws s3control create-access-point \
  --name secretcats \
  --account-id 123456789012 \
  --bucket catpics
```

**Parameters:**
* `--name`: Name of the access point (must be unique within the account)
* `--account-id`: AWS account ID that owns the bucket
* `--bucket`: Name of the bucket the access point is created for

### Use Cases

* **Multi-tenant applications** - Different access points for different tenants
* **Application isolation** - Separate access points for different applications accessing the same bucket
* **Network segmentation** - Different network controls (VPC-only vs public) for different access points
* **Compliance and security** - Isolate access patterns and apply different security policies
* **Team-based access** - Different access points for different teams with appropriate permissions
* **Data lake access** - Different access points for different analytics workloads

---

## Quick Exam Checklist – Day 8 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **KMS** | Regional; keys never leave KMS; symmetric/asymmetric; FIPS 140-2 L2; max 4KB per key op. |
| **DEK** | GenerateDataKey for >4KB; encrypt with plaintext key; store ciphertext key with data. |
| **Key policy** | Every key has one; key policy + IAM; aliases for key IDs. |
| **S3 Storage Classes** | Standard (frequent, important); Standard-IA (infrequent, 30d min); One Zone-IA (replaceable, 1 AZ); Glacier Instant (quarterly); Flexible (1–2/yr); Deep Archive (yearly); Intelligent-Tiering (unknown pattern). |
| **Lifecycle** | Transition (move class by age); Expiration (delete); versioning helps. |
| **Replication** | Versioning ON both sides; not retroactive; CRR vs SRR; SSE-C cannot replicate. |
| **S3 RTC** | 99.99% within 15 min SLA; CloudWatch metrics. |
| **PreSigned URL** | Temporary access; permissions of generator; don't use with role (expiry). |
| **S3 Select / Glacier Select** | SQL-like server-side filter; less transfer. |
| **Event Notifications** | SNS, SQS, Lambda; Created, Delete, Restore, Replication. |
| **Object Lock** | WORM; new buckets only; versioning required; Retention (COMPLIANCE/GOVERNANCE) + Legal Hold. |
| **Access Points** | Per-bucket multiple endpoints; different policies/network controls. |

---

## Important Exam Points – Day 8

- **KMS**: Keys **never leave** KMS; **4KB limit** per Encrypt/Decrypt; use **GenerateDataKey** for large data.
- **S3 Standard** = frequent, important; **Standard-IA** = infrequent + retrieval fee + 30d/128KB min; **One Zone-IA** = replaceable, 1 AZ.
- **Replication**: **Versioning required** on source and destination; **not retroactive** (batch replicate for existing).
- **Object Lock**: **New buckets only**; **COMPLIANCE** = no bypass; **GOVERNANCE** = bypass with s3:BypassGovernanceRetention.
- **PreSigned URL**: Don't generate with **role** (temp creds expire); use IAM user or long-term creds for longer expiry (up to 7 days).
- **S3 Access Points**: One bucket, many endpoints; different policies and network (e.g. VPC-only) per access point.

### Benefits

* **Granular access control** - Fine-grained permissions per access point
* **Network isolation** - Different network restrictions per access point
* **Simplified management** - Easier to manage than complex bucket policies
* **Better security** - Principle of least privilege per access point
* **Audit and monitoring** - Track access patterns per access point
* **Flexibility** - Easy to add or remove access points as needs change
