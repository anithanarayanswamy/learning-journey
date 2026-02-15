## AWS Relational Database Service (RDS)

### What is RDS?

Amazon Relational Database Service (RDS) is a **managed relational database service** that makes it easier to set up, operate, and scale relational databases in AWS. AWS manages the **database infrastructure**, allowing you to focus on schema design, queries, and application logic.

Supported engines:

* Amazon Aurora (MySQL-compatible & PostgreSQL-compatible)
* MySQL
* PostgreSQL
* MariaDB
* Oracle
* Microsoft SQL Server

---

### What AWS Manages vs What You Manage

**AWS manages:**

* OS installation & patching
* Database engine installation & patching
* Automated backups
* Point-in-time recovery
* Monitoring (CloudWatch)
* High availability (Multi-AZ)
* Hardware provisioning & replacement

**You manage:**

* Database schema
* Queries
* Indexes
* Database users
* Parameter tuning (via parameter groups)

---

### RDS Deployment Options

#### Single-AZ

* One DB instance
* Lowest cost
* ❌ Not highly available
* Used for dev/test or non-critical workloads

#### Multi-AZ (High Availability)

* Synchronous replication to a standby instance
* Automatic failover
* Standby **cannot** be used for reads
* Same endpoint after failover

**Exam Tip:** Multi-AZ is for **availability**, not performance.

---

### Read Replicas (Scalability)

* Asynchronous replication
* Used for **read scaling**
* Can be promoted to standalone DB
* Supported for MySQL, PostgreSQL, MariaDB, Aurora

Key points:

* Read replicas have their **own endpoint**
* Used for reporting, analytics, read-heavy apps
* Replication lag is possible

**Exam Tip:** Read Replicas = **performance**, Multi-AZ = **availability**

---

### Backups & Recovery

#### Automated Backups

* Enabled by default
* Retention: 1–35 days
* Stored in S3
* Supports point-in-time recovery

#### Manual Snapshots

* User-initiated
* Retained until deleted
* Can be copied across regions/accounts

**Exam Tip:** Deleting an RDS instance deletes automated backups, **not manual snapshots**.

---

### Security in RDS

#### Network Security

* Runs inside a VPC
* Controlled using Security Groups
* No public access unless explicitly enabled

#### Encryption

* Encryption at rest using KMS
* Encryption in transit using SSL/TLS

#### Authentication

* Traditional DB credentials
* IAM Database Authentication (MySQL & PostgreSQL)

---

### Monitoring & Maintenance

* Amazon CloudWatch metrics (CPU, storage, connections)
* Enhanced Monitoring (OS-level metrics)
* Maintenance windows for patching

---

### RDS Storage Types

* General Purpose SSD (gp2/gp3)
* Provisioned IOPS (io1/io2) – high-performance workloads
* Magnetic (legacy)

**Exam Tip:** Provisioned IOPS is used for **high, predictable I/O workloads**.

---

### RDS vs Aurora (Quick Comparison)

| Feature     | RDS           | Aurora                         |
| ----------- | ------------- | ------------------------------ |
| Performance | Standard      | Up to 5x MySQL / 3x PostgreSQL |
| Storage     | Fixed size    | Auto-scales up to 128 TB       |
| Replication | Read Replicas | 6-way replication across AZs   |
| Cost        | Lower         | Higher but more scalable       |

---

### High-Value Exam Points (SAA-C03)

* Multi-AZ = High Availability (not reads)
* Read Replicas = Read Scaling
* Automated backups allow point-in-time recovery
* Manual snapshots persist after deletion
* Security Groups control access, not NACLs
* RDS cannot be accessed via SSH
* Storage scaling is supported (no downtime for most engines)
* IAM authentication removes need for password storage

---

### Common Exam Traps

❌ Using Multi-AZ for read scaling
❌ Expecting read replicas to be synchronous
❌ Assuming standby DB is readable
❌ Forgetting snapshots before deletion

---

### When to Choose RDS

* Need relational database
* Want managed backups & HA
* Don’t want to manage OS or DB patching
