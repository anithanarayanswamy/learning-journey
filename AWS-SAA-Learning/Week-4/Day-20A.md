## Serverless, Messaging & API Gateway – SAA-C03 Notes

---

## What Is Serverless?

Serverless is an **architecture style**, not a single service.

### Core ideas

* You manage **few or no servers**
* Very **low operational overhead**
* Applications are built from **small, specialised components**

### Key characteristics

* **Stateless** and **ephemeral** environments
* Billed by **duration or usage**, not uptime
* **Event-driven** execution
* Consumption only when used

### Design philosophy

* Use **FaaS** for compute where possible
* Use **managed services** instead of self-managed infrastructure

📌 **Exam trigger**

If the question mentions *low ops*, *pay per use*, *event-driven* → think **Serverless**

---

## Simple Notification Service (SNS)

SNS is a **fully managed, public AWS messaging service**.

### Core concepts

* Public AWS service with **public endpoints**
* Coordinates **sending and delivery of messages**
* Message size: **≤ 256 KB**

### SNS Topics

* A **Topic** is the core SNS resource
* Defines:

  * Permissions
  * Configuration

### Publishers & Subscribers

* **Publishers** send messages to a **Topic**
* **Subscribers** receive messages from the topic

### Supported subscribers

* HTTP / HTTPS
* Email (JSON)
* SQS
* Lambda
* Mobile push
* SMS

### Usage

* Widely used for notifications across AWS
* Examples:

  * CloudWatch alarms
  * CloudFormation events

---

## SNS – Reliability & Security

### Delivery features

* Delivery status tracking (HTTP, Lambda, SQS)
* Automatic **retries** for reliable delivery

### Architecture

* **Highly available**
* **Regionally scalable**

### Security

* Server-Side Encryption (SSE)
* Cross-account access via **Topic Policies**

---

## Problems with Lambda

Lambda is powerful, but not always the best tool.

### Key limitations

* Lambda is **FaaS** only

* **15-minute maximum execution time**

* Functions can be chained, but:

  * Workflows can become complex
  * Harder to manage at scale

* Runtime environments are **stateless**

📌 **Exam clue**

If execution time > 15 minutes → **Lambda is NOT the answer**

---

## Step Functions – State Machines

Used to manage **serverless workflows**.

### Core concepts

* Workflow pattern:

  * **START → STATES → END**

* A **State** represents a unit of work

### Features

* Maximum execution duration: **1 year**
* Two workflow types:

  * **Standard**
  * **Express**

### Integration

State machines can be started by:

* API Gateway
* EventBridge
* IoT Rules
* Lambda

### Definition & permissions

* Defined using **Amazon States Language (ASL)** (JSON)
* Permissions granted using **IAM Roles**

---

## API Gateway – Overview

API Gateway is used to **create, manage, and expose APIs**.

### Core responsibilities

* Entry point for applications
* Sits between **clients and backend services**

### Key features

* Highly available and scalable

* Handles:

  * Authentication & authorisation
  * Throttling
  * Caching
  * CORS
  * Transformations
  * OpenAPI support

* Can integrate with:

  * AWS services
  * On-premises systems

### API types

* HTTP APIs
* REST APIs
* WebSocket APIs

---

## API Gateway – Errors (Exam Gold ⭐)

### Client vs Server errors

* **4XX** → Client error (bad request)
* **5XX** → Server / backend error

### Common status codes

* **400** – Bad Request (generic)
* **403** – Access Denied (authorizer / WAF)
* **429** – Throttled (rate exceeded)
* **502** – Bad Gateway (bad Lambda response)
* **503** – Service Unavailable (backend offline)
* **504** – Integration timeout (**29s limit**)

📌 **Exam hint**

* 4XX → client problem
* 5XX → backend / Lambda / integration problem

---

## Final Exam Triggers – Day 20

* Event-driven, low ops → **Serverless**
* Fan-out notifications → **SNS**
* Decoupled processing → **SQS**
* Long-running workflows → **Step Functions**
* API front door → **API Gateway**
* Lambda timeout issues → **Step Functions / ECS**
* 429 errors → **API Gateway throttling**
