## 1. VPC Flow Logs

### What VPC Flow Logs Capture

* Capture **metadata only** (**NOT packet contents**)
* Record traffic that is **ACCEPTED or REJECTED**
* **NOT real-time** (there is a delivery delay)

### Where Flow Logs Can Be Attached

* **VPC** → captures traffic for **all ENIs** in the VPC
* **Subnet** → captures traffic for **all ENIs** in the subnet
* **ENI** → captures traffic for a **specific ENI**

### Log Destinations

* **Amazon S3**
* **Amazon CloudWatch Logs**
* **Athena** can be used to query logs stored in S3

### Flow Log Record Fields (as shown)

* version
* account-id
* interface-id
* srcaddr / dstaddr
* srcport / dstport
* protocol
* packets / bytes
* start / end
* action (ACCEPT / REJECT)
* log-status

### Protocol Numbers

* **ICMP = 1**
* **TCP = 6**
* **UDP = 17**

### Traffic NOT Recorded in Flow Logs

* Traffic to/from **169.254.169.254**
* Traffic to/from **169.254.169.123**
* **DHCP** traffic
* **Amazon DNS server** traffic
* **Amazon Windows license activation** traffic

### Exam Important Points

* Flow Logs are for **visibility and troubleshooting**, not packet inspection
* Flow Logs help identify **ACCEPT vs REJECT** decisions

---

## 2. Egress-Only Internet Gateway

### IPv4 Behavior

* IPv4 addresses can be **private or public**
* **NAT** allows **private IPv4** addresses to access **public networks**
* NAT does **NOT** allow externally initiated inbound connections

### IPv6 Behavior

* With IPv6, **all IP addresses are public**
* **Internet Gateway (IPv6)** allows **INBOUND and OUTBOUND** traffic
* **Egress-Only Internet Gateway** allows **OUTBOUND-ONLY** traffic for IPv6

### Exam Important Points

* NAT is an **IPv4-only concept**
* Egress-Only IGW exists **only for IPv6**

---

## 3. Gateway Endpoints

### Core Characteristics

* Provide **private access** to:

  * **Amazon S3**
  * **Amazon DynamoDB**
* Use a **Prefix List** added to a **Route Table**
* **Highly Available (HA)** across all AZs in a Region by default
* **Regional** service (cannot access cross-region services)

### Endpoint Policies

* Endpoint policies control **what the endpoint can access**

### Security Pattern (From Slides)

* Used to **prevent leaky S3 buckets**
* S3 buckets can be configured to allow access **ONLY via the Gateway Endpoint**

### Access Rules

* Gateway Endpoints are **NOT accessible outside the VPC**
* Public IP addressing is **NOT required** for S3/DynamoDB access

### Exam Important Points

* Gateway Endpoints are **route-table based**
* Only **S3 and DynamoDB** are supported

---

## 4. Interface Endpoints

### Core Characteristics

* Provide **private access to AWS public services**
* Historically supported services **other than S3 and DynamoDB**
* Added to **specific subnets** as an **ENI**
* **NOT HA by default**

### High Availability Requirement

* For HA: **one endpoint per subnet per AZ**

### Network Control

* Network access controlled using **Security Groups**
* **TCP and IPv4 ONLY**

### Endpoint Policies

* Endpoint policies restrict **what actions can be performed**

### DNS Behavior

* Endpoint provides a **new service endpoint DNS name**
* Supports:

  * **Regional DNS**
  * **Zonal DNS**
* **Private DNS** overrides the default public DNS for the service

### Technology Used

* Uses **AWS PrivateLink**

### Exam Important Points

* Interface Endpoints are **ENI-based**
* HA requires **multiple endpoints across AZs**

---

## 5. VPC Peering

### Core Characteristics

* **Direct encrypted network link** between **two VPCs**
* Works:

  * Same-region or cross-region
  * Same-account or cross-account

### Routing & Security

* **Routing configuration is required** on both sides
* **Security Groups and NACLs** still apply
* Same-region Security Groups can **reference peer Security Groups**

### Critical Limitations

* **NO transitive peering**

  * A → B → C communication does **NOT** work
* **Overlapping CIDRs are NOT allowed**

### Traffic Behavior

* Traffic is **encrypted**
* Cross-region peering uses the **AWS global network**

### Exam Important Points

* VPC Peering is **point-to-point only**
* CIDR planning is critical

---

## 6. High-Probability Exam Triggers (From Slides Only)

* "Traffic metadata, not contents" → VPC Flow Logs
* "IPv6 outbound-only internet access" → Egress-Only Internet Gateway
* "Private access to S3 without internet" → Gateway Endpoint
* "Private access to AWS public services" → Interface Endpoint
* "Two VPCs only, no transitive routing" → VPC Peering

---
