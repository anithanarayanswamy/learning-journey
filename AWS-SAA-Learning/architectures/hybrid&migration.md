## 1. Hybrid Connectivity Foundations

### Site‑to‑Site VPN (IPsec)

* Encrypted **IPsec tunnel** over the **public internet**
* Uses **Customer Gateway (CGW)** ↔ **Virtual Private Gateway (VGW)** or **Transit Gateway (TGW)**
* Fast to deploy (minutes–hours)
* Supports **BGP** (route‑based VPN preferred)
* Typical throughput ~**1.25 Gbps** per tunnel
* Variable latency

**Use when**

* Quick hybrid connectivity is required
* As a **backup for Direct Connect**
* Encryption over the internet is acceptable

---

### AWS Direct Connect (DX)

* **Dedicated private physical connection** to AWS
* 1 / 10 / 100 Gbps links
* **Low, consistent latency**
* No encryption by default (IPsec VPN can be layered on top)
* Long lead time, higher cost

**Critical exam fact**: **DX has NO built‑in HA** — resilience must be designed.

**Use when**

* Predictable latency is required
* Large, steady data transfer volumes
* Hybrid workloads are long‑term

---

### Transit Gateway (TGW)

* Central **hub‑and‑spoke** routing service
* Supports **transitive routing**
* Connects:

  * VPCs
  * VPNs
  * Direct Connect
* Can be shared across accounts (AWS RAM)
* Supports cross‑region peering

**Use when**

* Many VPCs need centralized routing
* You want to avoid VPC‑to‑VPC mesh complexity

---

## 2. Hybrid Storage – AWS Storage Gateway

### What Storage Gateway Is

* On‑prem **VM or hardware appliance**
* Bridges on‑prem storage with AWS (**S3, EBS, Glacier**)

---

### Gateway Modes

#### File Gateway

* Exposes **NFS / SMB** shares
* Files stored as **objects in S3**
* Local caching for performance
* Multiple gateways can point to the same bucket
* **No object locking** → control write access carefully

**Use when**

* On‑prem apps need file shares
* Data should live in S3

---

#### Volume Cached

* Primary data in **S3**
* Frequently accessed data cached on‑prem
* Extends storage capacity

**Use when**

* On‑prem storage is limited
* Large datasets with hot/cold access patterns

---

#### Volume Stored

* Primary data stays **on‑prem**
* AWS stores **EBS snapshots**

**Use when**

* Backup and disaster recovery

---

#### Tape Gateway

* Virtual tape library (iSCSI)
* Backed by **S3 and Glacier**

**Use when**

* Replacing physical tape infrastructure

---

## 3. Large‑Scale Data Migration – AWS Snow Family

### Snowball

* Storage only
* 50 TB or 80 TB
* Encrypted with **KMS**
* Economical for **10 TB – 10 PB**

### Snowball Edge

* Storage **+ compute**
* EC2 and optional GPU
* Ideal for **edge processing before upload**

### Snowmobile

* Truck‑based data center
* Up to **100 PB**
* Best for **single‑site massive migrations**

---

## 4. Directory Services in Hybrid Environments

### Core Directory Concepts

* Stores users, groups, computers
* Structured as **domains → trees → forests**
* Central authentication & authorization

---

### AWS Managed Microsoft AD

* Full **Microsoft AD DS**, AWS‑managed
* Deployed **Multi‑AZ in a VPC**
* Supports **trusts with on‑prem AD**
* Resilient even if VPN fails

**Use when**

* Applications require real Microsoft AD
* Trust relationships are required

---

### AD Connector

* **Proxy only** to on‑prem AD
* No directory data stored in AWS
* Requires VPN or Direct Connect
* Fails if connectivity fails

**Use when**

* Identities must stay on‑prem

---

### Simple AD

* Samba‑based directory
* No trust relationships
* Small (≤500) or Large (≤5,000 users)

**Use when**

* Simple, cloud‑only directory

---

## 5. Online Data Migration – AWS DataSync

* High‑speed data transfer **TO / FROM AWS**
* Preserves metadata (permissions, timestamps)
* Built‑in data validation
* Incremental and scheduled sync
* ~**10 Gbps per agent**
* Integrates with **S3, EFS, FSx, NFS, SMB**

**Use when**

* Ongoing migrations
* DR / BC replication
* Network connectivity exists

---

## 6. Managed File Systems in AWS

### FSx for Windows File Server

* Native **Windows file system**
* Accessed via **SMB**
* Integrates with **AD**
* Supports DFS, VSS, backups
* Single‑AZ or Multi‑AZ

**Use when**

* Windows workloads expect NTFS / SMB

---

### FSx for Lustre

* High‑performance **HPC file system**
* Linux / POSIX workloads
* Hundreds of GB/s throughput

**Deployment Types**

* **Scratch**: temporary, no HA, no replication
* **Persistent**: replicated within one AZ, self‑healing, S3 backups

**Use when**

* Machine learning
* Big data analytics
* Financial modeling

---

## 7. AWS Transfer Family

* Managed file transfer service
* Transfers **TO / FROM S3 or EFS**

### Supported Protocols

* FTP (unencrypted)
* FTPS (TLS)
* SFTP (SSH)
* AS2 (B2B structured data)

### Key Rules (Exam‑Heavy)

* FTP: **VPC‑only, not public**
* AS2: VPC internal or internet only
* Charged per **server‑hour + data transferred**

---

## 8. End‑to‑End Hybrid & Migration Decision Flow

1. **Need connectivity fast?** → Site‑to‑Site VPN
2. **Need predictable latency?** → Direct Connect
3. **Many VPCs?** → Transit Gateway
4. **File access backed by S3?** → File Gateway
5. **Ongoing sync?** → DataSync
6. **No network?** → Snowball / Snowmobile
7. **Windows file shares?** → FSx for Windows
8. **HPC workloads?** → FSx for Lustre
9. **Legacy FTP clients?** → Transfer Family

---

## 9. VERY IMPORTANT EXAM TOPICS

### Must‑Know Cold

* Direct Connect **has no HA by default**
* Route‑based VPN supports **BGP**
* File Gateway **does NOT support object locking**
* DataSync **preserves metadata**
* AD Connector **fails if VPN/DX fails**
* FSx for Lustre Scratch **has no replication**
* Transfer Family FTP **cannot be public**

### High‑Probability Exam Triggers

* "Encrypted over the internet" → Site‑to‑Site VPN
* "Consistent latency" → Direct Connect
* "Central routing" → Transit Gateway
* "Replace tape" → Tape Gateway
* "Ongoing sync" → DataSync
* "No network" → Snowball
* "Windows SMB" → FSx for Windows
* "HPC / POSIX" → FSx for Lustre
* "Legacy SFTP to S3" → Transfer Family

---
