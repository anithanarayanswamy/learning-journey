## RDS, Aurora, DMS, EFS, Load Balancers & Auto Scaling – SAA-C03 Notes

---

### Amazon RDS – Core Concepts

Amazon RDS (Relational Database Service) is a **managed relational database service**.

* **Database as a Service (DBaaS)**

  * AWS manages:

    * OS
    * Database engine patching
    * Backups
    * Failover
  * You do **NOT** get OS or SSH access.

* **Supported engines**

  * MySQL
  * MariaDB
  * PostgreSQL
  * Oracle
  * Microsoft SQL Server

> **Important**: Amazon Aurora is a **separate product**, not just another RDS engine.

---

### RDS Architecture (High Level)

* One **DB instance** can host **one or more databases**.

* Each instance uses **dedicated EBS storage**.

* Runs inside a **VPC** using a **DB Subnet Group**.

* Can be:

  * **Private only** (VPC access)
  * **Publicly accessible** (internet access enabled)

* Backups & snapshots are stored in **S3**.

* Can be accessed from:

  * VPC resources
  * On‑premises via **VPN or Direct Connect**

---

### RDS Costs

You pay for RDS based on:

1. **Instance size & type**
2. **Single‑AZ vs Multi‑AZ**
3. **Storage type & amount**
4. **Data transfer**
5. **Backups & snapshots**
6. **Licensing** (Oracle / SQL Server)

---

### RDS Multi‑AZ – Instance Deployment

Multi‑AZ (Instance) is about **high availability**, not performance.

* One **Primary** DB instance
* One **Standby** replica
* **Synchronous replication**

  * Data is committed only when written to both

**Key points**

* Standby **cannot be used for reads or writes**
* **Extra cost** (not free tier)
* **ONE** standby only
* Same region, **different AZs**
* Failover time: **~60–120 seconds**

**Automatic failover triggers**

* AZ outage

* Primary failure

* Manual failover

* Instance type change

* OS or DB patching

* Backups are taken from the **standby** to reduce primary load

---

### RDS Multi‑AZ – Cluster Deployment

Cluster‑based Multi‑AZ (newer model) improves **performance and availability**.

* **1 Writer + 2 Readers** (different AZs)
* Uses:

  * Faster hardware
  * Graviton
  * Local NVMe SSD

**Replication model**

* Transaction‑log based replication
* Writes are **committed when at least one reader confirms**

**Benefits**

* Readers can handle **read traffic**
* Faster failover (~35 seconds + log replay)
* More efficient replication

**Endpoints**

* **Cluster endpoint** → Writer (reads, writes, admin)
* **Reader endpoint** → Load‑balanced reads
* **Instance endpoints** → Specific instance (testing/troubleshooting)

---

### RDS Backups & Restores

**Automated Backups**

* Continuous backups + transaction logs
* Point‑in‑time restore (5‑minute granularity)

**Snapshots**

* Manual
* Single point in time (snapshot creation time)

**Restore behavior**

* Always creates a **NEW DB instance**
* New endpoint
* Restore time can be **slow** → think **RTO**

---

### RDS Backups – Cross‑Region

* Automated backups can be replicated to another region
* Includes:

  * Snapshots
  * Transaction logs

**Notes**

* Not enabled by default
* Extra cost for:

  * Cross‑region transfer
  * Storage in destination region

---

### Read Replicas (RR)

Used for **performance and DR**, not HA.

* **Asynchronous replication**
* Up to **5 read replicas** per DB instance
* Replicas can have replicas (lag increases)

**Benefits**

* Read scaling
* Improved RPO

**Disaster recovery**

* Can be **promoted** to standalone DB
* Promotion is fast → **low RTO**
* Until promoted → **read‑only**

---

### RDS Security

**Encryption in transit**

* SSL/TLS supported
* Can be enforced

**Encryption at rest**

* Uses **EBS encryption + KMS**
* Encrypts:

  * Storage
  * Logs
  * Snapshots
  * Replicas

> Once enabled, encryption **cannot be removed**.

---

### Transparent Data Encryption (TDE)

* Supported for:

  * Oracle
  * Microsoft SQL Server

**Characteristics**

* Encryption handled **inside the DB engine**
* Can integrate with **CloudHSM** (Oracle)
* Removes AWS from chain of trust

---

### RDS IAM Authentication

* IAM is used for **authentication**, not authorization
* Authorization is handled by **DB engine users**

**How it works**

* IAM generates a **temporary auth token** (15 minutes)
* Token replaces DB password
* IAM policies map IAM identities to DB users

---

### RDS Custom

Bridges the gap between **RDS** and **DB on EC2**.

* Supports:

  * Oracle
  * Microsoft SQL Server

**Features**

* SSH / RDP / Session Manager access
* More OS & DB control
* Still uses RDS automation

**Workflow**

* Pause automation → customize
* Resume automation → production

---

### Aurora – Storage Architecture

Aurora uses a **distributed storage system**.

* All SSD

* High IOPS, low latency

* Storage automatically scales

* Billed on **actual usage**

* Data stored as **6 copies across AZs**

* Replicas can be added/removed without storage provisioning

---

### Aurora Cost Model

* No free tier
* No micro instances

**Billing**

* Compute: per‑second (10‑minute minimum)
* Storage: GB‑month used
* IO requests charged
* Backups: **100% of DB size included**

---

### Aurora Restore, Clone & Backtrack

* Backups similar to RDS
* Restores create **new clusters**

**Advanced features**

* **Backtrack**: in‑place rewind
* **Fast clone**: copy‑on‑write (very fast)

---

### Aurora Serverless

* Uses **Aurora Capacity Units (ACUs)**
* Define min & max ACUs
* Scales automatically
* Can scale to **zero**

**Billing**

* Per‑second
* Pay only while running

**Use cases**

* Infrequent workloads
* Dev / test
* Spiky or unpredictable traffic
* Multi‑tenant apps

---

### Aurora Global Database

Used for **global DR and read scaling**.

* Replication latency: ~1 second
* Up to **5 secondary regions**
* Each secondary can have **16 replicas**
* Secondary regions can be promoted to R/W

---

### Aurora Multi‑Master

* Default Aurora = single writer
* Multi‑master:

  * All instances are read/write

**Trade‑off**

* No writer failover
* Higher write contention

---

### RDS Proxy

Used to improve **connection management**.

**When to use**

* Too many DB connections
* Lambda functions
* Small / burst EC2 instances
* Long‑lived connections
* Faster failover

**Key facts**

* Fully managed
* Highly available
* Connection pooling
* VPC‑only access
* Reduces failover time by ~60%

---

### Database Migration Service (DMS)

Used to migrate databases **to AWS**.

* Uses a **replication instance**

* Requires:

  * Source endpoint
  * Target endpoint

* One endpoint **must be on AWS**

**Migration types**

* Full load
* Full load + CDC
* CDC only

---

### Schema Conversion Tool (SCT)

Used when **changing DB engines**.

* Not needed for same‑engine migrations
* Supports:

  * OLTP
  * OLAP
  * DB → S3

---

### DMS with Snowball

Used for **large (multi‑TB) migrations**.

1. Extract data using SCT
2. Copy to Snowball
3. Ship to AWS → load into S3
4. DMS migrates from S3 to target DB
5. CDC keeps data in sync

---

### Elastic File System (EFS)

* Managed **NFSv4** file system
* **Linux only**
* Shared storage
* Mounted by many EC2 instances

**Performance modes**

* General Purpose (default)
* Max I/O

**Storage classes**

* Standard
* Infrequent Access

---

### AWS Backup

Centralized backup service.

* Backup plans
* Backup vaults (KMS encrypted)
* Vault Lock (WORM)
* On‑demand backups
* PITR supported

---

### Load Balancer Architecture

* Runs in **2+ AZs**
* DNS points to LB nodes
* Nodes scale automatically
* Internet‑facing or internal
* Instances do not need public IPs

---

### Application Load Balancer (ALB)

* Layer 7 (HTTP/HTTPS)
* Content‑based routing
* SSL always terminated
* Application‑aware health checks

---

### Network Load Balancer (NLB)

* Layer 4 (TCP/UDP/TLS)
* Extremely fast
* Static IPs supported
* Supports unbroken encryption

---

### Auto Scaling Groups (ASG)

* Automatic scaling
* Self‑healing
* Uses launch templates

**Scaling types**

* Manual
* Scheduled
* Dynamic

---

### Final Exam Triggers

* HA for RDS → Multi‑AZ
* Read scaling → Read Replicas
* No servers → Aurora Serverless
* Global DR → Aurora Global Database
* Too many DB connections → RDS Proxy
* Large DB migration → DMS + SCT
