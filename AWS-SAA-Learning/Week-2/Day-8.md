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

