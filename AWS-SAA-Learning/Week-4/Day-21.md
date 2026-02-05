## Simple Queue Service (SQS) – Core Notes (SAA-C03)

SQS is a **public, fully managed, highly available queue service** used to **decouple** components and enable **asynchronous processing**.

---

### Queue Types

* **Standard Queue**

  * **At-least-once** delivery
  * Messages may be delivered **more than once**
  * **Best-effort ordering** (order not guaranteed)
  * Near **unlimited throughput**
  * Used for scale-first workloads

* **FIFO Queue**

  * **Exactly-once** processing
  * **Strict ordering** (First-In-First-Out)
  * Throughput:

    * **300 msg/s** without batching
    * **3,000 msg/s** with batching
  * Queue name **must end with `.fifo`**
  * Used when **order matters**

---

### Message Basics

* Maximum message size: **256 KB**
* Large payloads:

  * Store data in S3
  * Send S3 object reference via SQS
* Billed per **request**

  * 1 request = **1–10 messages** (up to 64 KB total)

---

### Message Lifecycle

1. Producer sends message to queue
2. Consumer receives message
3. Message becomes **hidden** (Visibility Timeout)
4. Consumer:

   * Deletes message → success
   * Does nothing → message reappears (retry)

---

### Visibility Timeout

* Time during which a message is **invisible** after being read
* Default: **30 seconds**
* Range: **0 seconds to 12 hours**
* Can be:

  * Set at queue level
  * Changed per message using `ChangeMessageVisibility`

📌 **Key idea**
Visibility Timeout ≠ Delay Queue

---

### Delay Queues

* Messages are **not visible immediately after being sent**
* Controlled by **DelaySeconds**
* Range:

  * Minimum: **0 seconds**
  * Maximum: **15 minutes**
* Can be:

  * Queue-level delay
  * Per-message delay (overrides queue setting)
* ❌ Not supported on FIFO queues (per-message delay)

---

### Polling Types

* **Short Polling**

  * Immediate response
  * May return no messages
* **Long Polling**

  * Waits up to `waitTimeSeconds`
  * Reduces empty responses
  * Lower cost, better efficiency

📌 **Exam hint**
Cost optimization + efficiency → **Long polling**

---

### Security

* Encryption **in transit**
* Encryption **at rest** using **KMS**
* Access controlled via **Queue Policies**

---

### Dead-Letter Queues (DLQ)

Used to isolate **problem messages**.

* Defined using a **redrive policy**
* Specifies:

  * Source queue
  * Dead-letter queue
  * `maxReceiveCount`

### DLQ Flow

* Message is received but not deleted
* `ReceiveCount` increases
* When `ReceiveCount > maxReceiveCount`:

  * Message is moved to the **DLQ**

Key points:

* Original enqueue timestamp is **preserved**
* DLQ retention period is usually **longer**
* Helps with debugging and replay

---

## SQS Standard vs FIFO (At a Glance)

| Feature    | Standard           | FIFO                   |
| ---------- | ------------------ | ---------------------- |
| Delivery   | At-least-once      | Exactly-once           |
| Ordering   | Best effort        | Strict                 |
| Throughput | Near unlimited     | Limited                |
| Duplicates | Possible           | Removed                |
| Use case   | Scale & decoupling | Ordering & correctness |

---

## Kinesis – Core Concepts

Kinesis is a **scalable streaming service** designed for **real-time data ingestion**.

---

### Key Characteristics

* Producers **send records** into a stream
* Public AWS service
* Highly available by design
* Designed for **very high throughput**
* Streams scale from low to **near-infinite data rates**

---

### Data Retention

* Default retention: **24 hours**
* Can be increased up to **365 days** (extra cost)
* Multiple consumers can read the **same data window**

📌 **Important distinction**
Kinesis stores data for a **time window**
SQS does **not** persist messages once consumed

---

## SQS vs Kinesis

### SQS

* 1 producer group → 1 consumer group
* **Decoupling** & async processing
* No data window
* Messages disappear after processing

### Kinesis

* Designed for **massive ingestion**
* Supports **multiple consumers**
* Uses a **rolling data window**
* Ideal for:

  * Analytics
  * Monitoring
  * Clickstreams
  * Streaming pipelines

---

## Kinesis Data Firehose

Kinesis Data Firehose is a **fully managed, serverless delivery service**.

---

### Purpose

* Load streaming data into:

  * Data lakes
  * Data stores
  * Analytics services

Destinations include:

* S3
* Redshift
* OpenSearch
* HTTP endpoints (e.g., Splunk)

---

### Key Features

* Fully managed & serverless
* **Automatic scaling**
* Highly resilient
* Near real-time delivery (**~60 seconds**)
* Optional **Lambda transformation** of data
* Billed based on **data volume ingested**

---

### Delivery Behavior

* Data is delivered when:

  * Buffer size (MB) is reached
  * OR buffer interval (~60s) expires
* Uses S3 as:

  * Backup location
  * Intermediate storage for Redshift (COPY)

---

### Architecture Summary

* Producers can:

  * Send data directly to Firehose
  * Send data to Kinesis Data Streams
* Firehose can:

  * Read from streams
  * Transform data with Lambda
  * Deliver to final destinations

---

## Exam Triggers (High Value)

* Decoupling + async → **SQS**
* Ordering matters → **SQS FIFO**
* Retry + failure handling → **DLQ**
* High-scale ingestion → **Kinesis**
* Multiple consumers + replay → **Kinesis**
* Load streaming data into S3/Redshift → **Firehose**
* Near real-time delivery, no consumers to manage → **Firehose**

---

### One-line Memory Hooks

* **SQS** = queue + decoupling + retries
* **Kinesis** = streams + window + scale
* **Firehose** = deliver streams to destinations automatically
## Kinesis Data Analytics – Core Notes (SAA-C03)

Kinesis Data Analytics is used for **real-time processing of streaming data** using **SQL**.

---

### What It Does

* Performs **real-time analytics** on streaming data
* Uses **Structured Query Language (SQL)**
* No infrastructure to manage (serverless processing layer)

---

### Data Sources

Kinesis Data Analytics can ingest data from:

* **Kinesis Data Streams**
* **Kinesis Data Firehose**

---

### Destinations / Outputs

Processed data can be sent to:

* **Kinesis Data Firehose**

  * S3
  * Redshift
  * OpenSearch / Elasticsearch
  * Splunk
* **AWS Lambda**
* **Kinesis Data Streams** (for further processing)

---

### When to Use It

* Streaming data that requires **real-time SQL processing**
* **Time-series analytics**

  * Elections
  * E-sports
* **Real-time dashboards**

  * Game leaderboards
* **Real-time metrics**

  * Security monitoring
  * Incident response teams

📌 **Exam trigger**
“Real-time SQL on streams” → **Kinesis Data Analytics**

---

## Kinesis Video Streams – Core Notes

Kinesis Video Streams is designed for **live video ingestion and processing**.

---

### What It Ingests

* **Live video data** from producers such as:

  * Security cameras
  * Smartphones
  * Cars
  * Drones
* Also supports time-serialised:

  * Audio
  * Thermal
  * Depth
  * RADAR data

---

### Key Characteristics

* Consumers can read data:

  * **Frame-by-frame**
  * Or on-demand
* Data can be:

  * **Persisted**
  * **Encrypted** (in transit and at rest)

⚠️ **Important limitation**

* Data **cannot be accessed directly from storage**
* Access is **only via APIs**

---

### Integrations

* Integrates with other AWS services such as:

  * Rekognition
  * Connect

---

## Amazon Cognito – Core Notes

Amazon Cognito provides **authentication, authorization, and user management** for web and mobile applications.

---

### Main Capabilities

* User sign-up and sign-in
* Secure identity management
* Token-based authentication

---

### User Pools

* Handle **authentication**
* Users sign in and receive a **JWT (JSON Web Token)**
* Features include:

  * User directory
  * Custom profiles
  * Customizable UI
  * MFA and security controls

---

### Identity Pools

* Provide **temporary AWS credentials**
* Used to access AWS resources securely

#### Identity Types

* **Authenticated users**
* **Unauthenticated (guest) users**
* **Federated identities**

  * Google
  * Facebook
  * Twitter
  * SAML 2.0
  * User Pools

📌 **Key idea**
User Pool = *Who you are*
Identity Pool = *What AWS access you get*

---

## AWS Glue – Core Notes

AWS Glue is a **serverless ETL service**.

---

### Purpose

* **Extract, Transform, Load (ETL)** data
* Move and transform data between **sources and destinations**
* Fully managed (no servers)

---

### Key Features

* Crawls data sources
* Automatically builds the **Glue Data Catalog**
* Used instead of Data Pipeline + EMR for serverless ETL

---

### Data Sources

* **Stores**

  * S3
  * RDS
  * JDBC-compatible databases
  * DynamoDB
* **Streams**

  * Kinesis Data Streams
  * Apache Kafka

---

### Data Targets

* S3
* RDS
* JDBC-compatible databases

---

## AWS Glue Data Catalog – Core Notes

The Glue Data Catalog stores **persistent metadata** about data sources.

---

### Key Characteristics

* **One catalog per region per account**
* Central metadata repository
* Helps **avoid data silos**

---

### Services That Use It

* Amazon Athena
* Redshift Spectrum
* EMR
* AWS Lake Formation

---

### Crawlers

* Crawlers scan data sources
* Automatically infer:

  * Schema
  * Partitions
  * Metadata

---

## Amazon MQ – Core Notes

Amazon MQ is a **managed message broker service** for migration scenarios.

---

### What It Is

* Managed **Apache ActiveMQ**
* Open-source message broker
* Supports:

  * Queues
  * Topics

---

### Supported APIs & Protocols

* **JMS API**
* AMQP
* MQTT
* OpenWire
* STOMP

---

### Architecture

* One-to-one or one-to-many messaging
* Deployment options:

  * **Single instance** (dev/test)
  * **HA pair** (active/standby)

⚠️ **Important**

* Runs inside a **VPC**
* **Not a public service**
* Requires **private networking**

---

### When to Use Amazon MQ

* Migrating existing systems to AWS
* Minimal application changes required
* Existing apps rely on:

  * JMS
  * AMQP
  * MQTT
  * STOMP

---

### When NOT to Use Amazon MQ

* New cloud-native designs
* When AWS-native integration is required

📌 **Default guidance**

* New applications → **SNS / SQS**
* Migration with legacy protocols → **Amazon MQ**

---

## Amazon AppFlow – Core Notes

Amazon AppFlow is a **fully managed integration service**.

---

### What It Does

* Exchanges data between applications using **connectors**
* Uses **flows** to move data
* No code required

---

### Key Capabilities

* Sync data across applications
* Aggregate data from multiple sources
* Works with **public endpoints**
* Supports **PrivateLink** for private connectivity
* Custom Connector SDK available

---

### Example Use Cases

* Salesforce contacts → Redshift
* Zendesk support tickets → S3

---

## High-Value Exam Triggers

* Real-time SQL on streams → **Kinesis Data Analytics**
* Live video ingestion → **Kinesis Video Streams**
* App authentication + JWT → **Cognito User Pools**
* Temporary AWS credentials → **Cognito Identity Pools**
* Serverless ETL → **AWS Glue**
* Central metadata store → **Glue Data Catalog**
* Legacy messaging migration → **Amazon MQ**
* SaaS-to-AWS data sync → **AppFlow**

---

### One-line Memory Hooks

* **Kinesis Data Analytics** = SQL on streams
* **Kinesis Video Streams** = live video ingestion
* **Cognito** = users + identity
* **Glue** = serverless ETL + catalog
* **Amazon MQ** = managed ActiveMQ for migration
* **AppFlow** = SaaS data integration
