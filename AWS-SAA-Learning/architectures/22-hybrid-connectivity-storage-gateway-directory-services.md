## 1. Hybrid Connectivity & Transit (VPN, DX, TGW)

* **Site-to-Site VPN**

  * Encrypted & authenticated **IPsec tunnel** over the **public internet**
  * Uses **CGW ↔ VGW/TGW public endpoints**
  * **Transit-agnostic** (internet or DX)
  * **Higher cryptographic overhead** → lower max throughput
  * **Fast to provision**, ideal as **DX backup** or interim connectivity

* **Direct Connect (DX)**

  * **Dedicated private connection** to AWS
  * **Low, consistent latency** and high bandwidth
  * **No encryption by default** (MACsec is single-hop only)
  * **Resilience is NOT automatic** — must be architected
  * Longer lead time, higher cost, predictable performance

* **Transit Gateway (TGW)**

  * Supports **transitive routing**
  * Enables **hub-and-spoke** and **global networks**
  * Can be **shared across accounts** using **AWS RAM**
  * Supports **cross-region peering**
  * **Lower operational complexity** vs VPC-to-VPC meshes

**Exam Triggers**

* *“Encrypted over internet”* → Site-to-Site VPN
* *“Consistent latency / private path”* → Direct Connect
* *“Central routing / many VPCs / scale”* → Transit Gateway

---

## 2. AWS Storage Gateway (Hybrid Storage)

* **What it is**

  * On-prem **VM or hardware appliance**
  * Bridges on-prem storage with **AWS (S3, EBS, Glacier)**

### Gateway Modes (Choose by data location)

* **Volume Stored**

  * **Primary data stays on-prem**
  * AWS stores **EBS snapshots**
  * Best for **full backups & disaster recovery**
  * Does **NOT** extend on-prem capacity

* **Volume Cached**

  * **Primary data in S3**
  * Frequently accessed data **cached on-prem**
  * Enables **capacity extension**
  * Optimized for large datasets with hot/cold access patterns

* **Tape Gateway (VTL)**

  * Emulates **iSCSI tape library**
  * Virtual tapes stored in **S3**, archived to **Glacier**
  * Designed for **large-scale, long-term backups**
  * Minimal change to existing backup software

* **File Gateway**

  * Presents **NFS/SMB shares**
  * Maps directly to **S3 buckets**
  * Files appear local but are **stored as S3 objects**
  * **Read/write caching** for LAN-like performance
  * Native integration with AWS services

**Exam Triggers**

* *“On-prem apps, cloud backup”* → Volume Stored
* *“Unlimited storage, local cache”* → Volume Cached
* *“Replace tape libraries”* → Tape Gateway
* *“File shares backed by S3”* → File Gateway

---

## 1. Hybrid Storage & Large-Scale Data Transfer

### **File Gateway (Advanced Patterns)**

* Multiple File Gateways can map to **the same S3 bucket**
* **No object locking** → risk of overwrite conflicts
* Use **read-only shares** or tightly controlled writers
* **NotifyWhenUploaded API** keeps gateway caches in sync
* **Primary data always lives in S3**
* Supports **S3 lifecycle policies** (Standard → IA → Glacier)
* **Cross-Region Replication (CRR)** works at the S3 layer, not the gateway

**Exam Triggers**

* *“Multiple on-prem writers to same data”* → File Gateway + access control
* *“Regional DR for file data”* → S3 CRR
* *“Tier data automatically over time”* → S3 Lifecycle

---

### **AWS Snow Family (Offline Data Transfer)**

**Snowball**

* **Storage only**
* 50 TB or 80 TB per device
* Encrypted with **AWS KMS**
* Ordered → delivered → loaded → returned
* Economical for **10 TB to 10 PB**
* Multiple devices, multiple sites supported

**Snowball Edge**

* **Storage + compute**
* Higher capacity than Snowball
* 10 / 25 / 45 / 50 / 100 Gbps networking
* EC2 + optional **GPU**
* Ideal for **edge processing before upload**

**Snowmobile**

* **Truck-based mobile data center**
* Up to **100 PB per unit**
* Best for **single-site, massive migrations**
* Not economical below ~10 PB

**Exam Triggers**

* *“No reliable network”* → Snowball / Snowball Edge
* *“Process data before upload”* → Snowball Edge
* *“Exabyte-scale migration”* → Snowmobile

---

## 2. Directory Services & Identity in AWS

### **Core Directory Concepts**

* Stores **users, groups, computers, servers**
* Structured as **domains → trees → forests**
* Centralized authentication & authorization
* Most common implementation: **Microsoft AD DS**
* Linux/open-source alternative: **Samba**

---

### **AWS Directory Service**

* **AWS-managed** directory infrastructure
* Deployed **inside a VPC**
* **Multi-AZ for HA**
* Can be:

  * Standalone (cloud-only)
  * Integrated with on-prem AD
  * Used as a **proxy** to on-prem AD
* Required by some services (e.g. **Amazon WorkSpaces**)

---

### **Simple AD**

* **Standalone directory using Samba**
* Supports **EC2 & WorkSpaces authentication**
* **Small (≤500 users)** or **Large (≤5,000 users)**
* **No trust or integration** with on-prem Microsoft AD
* Designed for **simple, cloud-only use cases**

**Exam Triggers**

* *“Need AD features, no on-prem integration”* → Simple AD
* *“Existing on-prem AD integration”* → AWS Managed Microsoft AD
* *“WorkSpaces authentication”* → Directory Service required

---

## 1. AWS Directory Service – Choosing the Right Mode

### **AWS Managed Microsoft AD**

* **Full Microsoft AD DS** managed by AWS
* Required when apps need **MS AD schema / schema extensions**
* Deployed **Multi-AZ inside a VPC**
* **Trust relationships** supported with on-prem AD
* **Resilient to VPN failure** (directory still runs in AWS)
* Supports **WorkSpaces, FSx, Windows workloads**
* Best choice for **enterprise AD in AWS**

**Use when**

* Apps **require real Microsoft AD**
* You need **trust with on-prem AD**
* You want **HA without managing DCs**

---

### **AD Connector**

* **Proxy only** — no directory data stored in AWS
* Authentication requests forwarded to **on-prem AD**
* Requires **VPN or Direct Connect**
* **Fails if connectivity fails**
* No local authentication capability

**Use when**

* You **must not store identities in AWS**
* You already have **on-prem AD**
* You just need AWS services (e.g. WorkSpaces) to authenticate

---

### **Simple AD**

* **Standalone directory** using Samba
* **No trust**, no on-prem integration
* Supports **EC2 & WorkSpaces**
* Small (≤500 users) or Large (≤5,000 users)
* Lowest cost, lowest capability

**Use when**

* **Simple, cloud-only** directory
* No advanced AD features required

---

### **Exam Decision Rule**

* *“Needs MS AD / trusts / schema changes”* → **AWS Managed Microsoft AD**
* *“Use existing AD, no cloud identities”* → **AD Connector**
* *“Basic directory, no integration”* → **Simple AD**

---

## 2. Data Movement & Windows File Services in AWS

### **AWS DataSync**

* **Online, high-speed data transfer** TO/FROM AWS
* Designed for **huge scale**
* Preserves **metadata** (permissions, timestamps)
* Built-in **data validation**
* **10 Gbps per agent** (~100 TB/day)
* Supports **incremental & scheduled sync**
* Compression + encryption (TLS)
* Integrates with **S3, EFS, FSx, NFS, SMB**
* Pay **per GB transferred**

**Use when**

* Ongoing **migrations, DR/BC, archival**
* You have **network connectivity**
* You need **repeatable, reliable sync**

---

### **FSx for Windows File Server**

* Fully managed **native Windows file system**
* Accessed over **SMB**
* Uses **Windows permission model**
* Integrates with **AWS Managed AD or self-managed AD**
* **Single-AZ or Multi-AZ**
* Supports **VSS (user-driven restores)**
* Supports **DFS namespaces**
* Built-in backups
* Accessible via **VPC, Peering, VPN, DX**
* High performance: **low latency, high IOPS**

**Use when**

* You need **Windows file shares in AWS**
* Applications expect **NTFS, SMB, AD integration**
* You don’t want to manage file servers

---

### **Exam Decision Rule**

* *“Move data continuously at scale”* → **DataSync**
* *“Windows native file shares”* → **FSx for Windows**
* *“Preserve permissions & metadata”* → **DataSync**
* *“User self-service file restore”* → **FSx (VSS)**

---

## 1. FSx for Lustre (HPC & Extreme Performance Storage)

* Fully managed **Lustre file system**
* Built for **HPC, Linux clients, POSIX workloads**
* Common use cases:

  * **Machine Learning**
  * **Big Data analytics**
  * **Financial modelling**
* **Hundreds of GB/s throughput**
* **Sub-millisecond latency**
* Accessible via **VPN or Direct Connect**

---

### **Deployment Types**

**Scratch**

* Designed for **pure performance**
* **Short-term / temporary workloads**
* **NO HA**
* **NO replication**
* Higher failure risk as size increases
* Best for **re-creatable data**
* Base performance: **200 MB/s per TiB**

**Persistent**

* Designed for **longer-term workloads**
* **Replication within ONE AZ**
* **Self-healing** on hardware failure
* Supports **backups to S3** (manual or automatic, 0–35 days)
* Performance tiers:

  * **50 / 100 / 200 MB/s per TiB**
* Can **burst up to 1,300 MB/s per TiB** (credit-based)

---

### **Architecture & Scaling Facts**

* Metadata stored on **Metadata Targets (MST)**
* Data stored on **Object Storage Targets (OSTs)** (~1.17 TiB each)
* Minimum size: **~1.2 TiB**
* Scales in **2.4 TiB increments**
* Performance **scales with size**

---

### **Exam Triggers**

* *“HPC / Linux / POSIX”* → **FSx for Lustre**
* *“Extreme throughput, very low latency”* → **FSx for Lustre**
* *“Temporary compute results”* → **Scratch**
* *“Longer-term HPC data”* → **Persistent**

---

## 2. AWS Transfer Family (Managed File Transfer)

* Managed **file transfer service**
* Moves data **TO/FROM S3 or EFS**
* Provides managed **protocol endpoints** (servers)
* Fully **Multi-AZ**, resilient, scalable

---

### **Supported Protocols**

* **FTP** – Unencrypted
* **FTPS** – TLS-encrypted FTP
* **SFTP** – File transfer over SSH
* **AS2** – Structured **B2B data exchange**

---

### **Identity & Access**

* Authentication options:

  * **Service-managed users**
  * **AWS Directory Service**
  * **Custom IdP** (Lambda + API Gateway)

---

### **Networking Rules (Very Exam-Heavy)**

* **FTP**

  * **VPC-only**
  * **Cannot be public**
* **FTPS**

  * Directory Service or Custom IdP only
* **AS2**

  * **VPC Internet or internal only**
* Charged:

  * **Per server-hour**
  * **Plus data transferred**

---

### **Managed File Transfer Workflows (MFTW)**

* Serverless workflow engine
* Automates **post-upload processing**
* Used to:

  * Integrate with **existing workflows**
  * Or create **new file pipelines**

---

### **Exam Triggers**

* *“Use existing FTP/SFTP clients with S3”* → **Transfer Family**
* *“B2B structured file exchange”* → **AS2**
* *“Can’t rewrite legacy integrations”* → **Transfer Family**
* *“Automated post-transfer processing”* → **MFTW**

---
