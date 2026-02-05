## EBS Volume Types & AWS Lambda – SAA-C03 Notes

---

## Elastic Block Store (EBS) – Volume Types Overview

Amazon EBS provides **persistent block storage** for EC2 instances.

* Data persists **independently of instance lifecycle**
* Volumes are **AZ-specific**
* Used for:

  * Boot volumes
  * Application data
  * Databases

---

## General Purpose SSD – gp3 (Very Important)

**gp3 is the default and recommended EBS volume type.**

### What is gp3?

* SSD-based EBS volume
* Balanced **price + performance**
* Suitable for **most workloads**

### Why gp3 is better than gp2

* Performance is **independent of volume size**
* You can configure **IOPS and throughput separately**
* **Cheaper** than gp2

### Best for

* Boot volumes
* Application servers
* Small to medium databases
* Dev / test workloads

📌 **Exam hint**

If the question mentions:

* *general purpose*
* *default volume*
* *no special performance requirements*

👉 **Choose gp3**

---

## Magnetic Volumes (Legacy / Old)

Also known as:

* Standard EBS volumes
* Magnetic-backed storage

### What are they?

* Old-generation EBS volumes
* HDD-based
* Very low performance

### Status today

* ❌ Not recommended
* ❌ Rarely used
* ❌ Mostly removed from new setups

### Why they appear in exams

* To test if you know they are **outdated**

📌 **Exam rule**

If performance matters → **DO NOT choose Magnetic**

---

## Provisioned IOPS SSD – io1 / io2

High-performance EBS volumes designed for **consistent and predictable I/O**.

### What does “Provisioned IOPS” mean?

* You specify exactly how many IOPS you need
* AWS **guarantees** that performance

### Types

* **io1** – older generation
* **io2** – newer, better durability, preferred

### Best for

* Large databases
* OLTP systems
* Mission-critical workloads
* Applications requiring **consistent low latency**

### Key features (Exam Gold ⭐)

* Highest IOPS
* Lowest latency
* Supports **Multi-Attach**
* Expensive 💰

📌 **Exam hint**

Keywords like:

* *mission-critical*
* *guaranteed performance*
* *very high IOPS*

👉 **Choose io2**

---

## EBS Snapshots & Automation

### Snapshot Basics

* Snapshots are **incremental**
* Stored in **S3** (managed by AWS)
* Can be used to:

  * Restore volumes
  * Create new volumes
  * Copy across regions

---

## Amazon Data Lifecycle Manager (DLM)

Used to **automate EBS snapshot creation and retention**.

### When to use DLM

* Only EBS snapshots are required
* Simple automation
* No compliance or multi-service needs

📌 **Final exam answer**

Use **Amazon DLM** to automate EBS snapshots.

---

## One-Glance Exam Rule – Backups

* **EBS snapshots only + simple automation** → Amazon DLM
* **Multi-service backup + compliance** → AWS Backup
* **Manual scripts** → almost never correct
* **Storage Gateway** → hybrid / on-prem use cases only

---

## AWS Lambda – Core Concepts

AWS Lambda is a **Function-as-a-Service (FaaS)** offering.

### What Lambda is

* Short-running, focused code
* No servers to manage
* Event-driven execution

### Key characteristics

* A **Lambda function** is a piece of code

* Runs using a **runtime** (e.g., Python, Node.js)

* Executed inside a **runtime environment**

* Memory allocation **indirectly controls CPU**

* Billed for **execution duration**

* Core building block of **serverless architectures**

---

## Common Lambda Use Cases

* Serverless applications (S3 + API Gateway + Lambda)
* File processing (S3 Events → Lambda)
* Database triggers (DynamoDB Streams → Lambda)
* Serverless CRON jobs (EventBridge + Lambda)
* Real-time stream processing (Kinesis + Lambda)

---

## Lambda Logging & Monitoring

### Logging

* Lambda logs go to **CloudWatch Logs**
* Requires permissions via **Execution Role**

### Metrics

Stored in **CloudWatch**:

* Invocations
* Success / failure
* Retries
* Duration / latency

### Tracing

* Lambda integrates with **X-Ray**
* Enables distributed tracing

---

## Lambda Versions & Aliases

### Versions

* Lambda functions support versions: v1, v2, v3…
* A version = **code + configuration**
* Versions are **immutable**
* Each version has its own **ARN**

### $LATEST

* Points to the latest unpublished version
* Changes whenever code is updated

### Aliases

* Named pointers (DEV, STAGE, PROD)
* Aliases point to **versions**
* Can be updated to shift traffic

---

## Lambda Execution Context & Cold Starts

### Execution Context

* Environment where a Lambda function runs
* May be **reused** across invocations

### Cold Start

* New execution context is created
* Includes runtime setup + code download
* Adds latency (~100ms)

### Warm Start

* Existing execution context reused
* Very fast (~1–2ms)

### Provisioned Concurrency

* Keeps execution contexts **warm and ready**
* Reduces cold start latency

---

## Final Exam Triggers – Day 20

* Default EBS volume → **gp3**
* Guaranteed IOPS → **io2**
* Legacy / outdated → **Magnetic**
* Automate EBS snapshots → **Amazon DLM**
* No servers, event-driven compute → **Lambda**
* Scheduled jobs without servers → **EventBridge + Lambda**
* Reduce cold starts → **Provisioned Concurrency**
