# AWS SAA-C03 Study Guide - Day 18: RDS, Aurora, and Load Balancers

---

## **RDS – Multi AZ – Instance**

### Concepts Explained:

• **Data => Primary AND Replicated to StandBy = Committed (Synchronous)**
  - When you write data to RDS Multi-AZ, it's written to the primary instance first
  - Simultaneously, the data is replicated to the standby instance in a different AZ
  - The write is only considered complete (committed) when BOTH instances have the data
  - This is synchronous replication, meaning it happens in real-time before confirming the write

• **Not free tier … extra cost for replica**
  - Multi-AZ deployment is not available in the free tier
  - You pay for the standby instance in addition to your primary instance
  - Approximately doubles your RDS instance costs

• **ONE StandBy replica ONLY**
  - In Multi-AZ instance mode, you can have exactly one standby replica
  - Cannot have multiple standby instances (unlike Aurora or Multi-AZ Cluster)

• **… which can't be used for reads or writes**
  - The standby replica is purely for high availability and disaster recovery
  - It does NOT serve any read or write traffic during normal operations
  - All application traffic goes to the primary instance only

• **60–120 seconds failover**
  - If the primary fails, RDS automatically fails over to the standby
  - The DNS name stays the same; it just points to the new primary
  - Typical failover time is 60-120 seconds

• **Same region only … different AZs in the same region**
  - Both primary and standby must be in the same AWS region
  - They are placed in different Availability Zones for fault isolation
  - This protects against AZ-level failures but not region-wide outages

• **Backups taken from standby to improve performance**
  - Automated backups and snapshots are taken from the standby instance
  - This reduces I/O impact on the primary instance
  - Improves performance for production workloads

• **AZ Outage, Primary Failure, Manual failover, Instance type change and software patching**
  - These are scenarios that trigger automatic failover to standby
  - Patching is applied to standby first, then failover occurs, then old primary is patched

### **🎯 Important Exam Points:**
- Multi-AZ is for **high availability**, NOT for scaling reads
- Failover is **automatic** and maintains the same endpoint
- **Synchronous replication** = no data loss (0 RPO for AZ failure)
- Standby is **NOT accessible** for any operations
- Use **Read Replicas** for read scaling, not Multi-AZ standby

---

## **RDS – Architecture**

### Concepts Explained:

• **Database as a Service (DBaaS)**
  - AWS manages the infrastructure, OS, patching, backups
  - You focus on your database and application logic

• **… Database Server as a Service (DBSaaS)**
  - RDS provides the entire database server, not just database storage
  - Includes compute, memory, storage, and networking

• **… multiple databases on one DB Server (instance)**
  - A single RDS instance can host multiple databases
  - For example, one MySQL RDS instance can have multiple schemas/databases
  - More cost-effective than creating separate instances for each database

• **Choice of DB Engines (MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server)**
  - RDS supports multiple database engines
  - Each has different features, licensing, and performance characteristics

• **… Amazon Aurora* is a different product**
  - Aurora is AWS's cloud-native database
  - Separate service from RDS with different architecture and pricing
  - MySQL and PostgreSQL compatible

• **Managed service … no access to OS or SSH Access***
  - You cannot SSH into RDS instances
  - No root/admin access to the underlying operating system
  - AWS manages OS-level operations and patches

### **🎯 Important Exam Points:**
- RDS is **fully managed** - no OS access
- One RDS instance = **multiple databases**
- Aurora is **not RDS** (different product, different features)
- If you need OS access → use **EC2 with database** or **RDS Custom**

---

## **RDS – Costs**

### Concepts Explained:

• **Cost #1 – Instance Size & Type**
  - Larger instances (more CPU/memory) cost more
  - Different instance families: T-series (burstable), M-series (general purpose), R-series (memory optimized)

• **Cost #2 – Multi AZ or not**
  - Multi-AZ roughly doubles instance costs
  - Single-AZ is cheaper but less available

• **Cost #3 – Storage type & amount**
  - General Purpose SSD (gp2/gp3) - balanced price/performance
  - Provisioned IOPS SSD (io1/io2) - high performance, higher cost
  - Storage scales from 20GB to 64TB depending on engine

• **Cost #4 – Data Transferred**
  - Data transfer out to the internet incurs charges
  - Data transfer between AZs (Multi-AZ replication) is free
  - Data transfer in is free

• **Cost #4 – Backups & Snapshots** (Note: should be Cost #5)
  - Automated backups within retention period = free (up to 100% of DB size)
  - Manual snapshots = charged for S3 storage
  - Snapshots beyond retention period are charged

• **Cost #5 – Licensing* (if applicable)** (Note: should be Cost #6)
  - Oracle and SQL Server may require license fees
  - License Included model: AWS provides license
  - BYOL (Bring Your Own License): you provide license

### **🎯 Important Exam Points:**
- Multi-AZ = **~2x instance cost**
- Backups within retention = **free** (up to 100% DB size)
- IOPS-heavy workloads → **Provisioned IOPS** (expensive)
- Cross-region data transfer = **charged**

---

## **RDS – Multi AZ – Cluster**

### Concepts Explained:

• **1 Writer and 2 Reader DB instances (different AZs)**
  - Multi-AZ Cluster mode has 1 writer + 2 readers (total 3 instances)
  - All three are spread across different AZs
  - Different from Multi-AZ Instance which has only 1 standby

• **… Much faster hardware, Graviton + local NVME SSD Storage**
  - Uses AWS Graviton processors (ARM-based, high performance)
  - Local NVMe SSDs provide extremely low latency
  - Better performance than standard Multi-AZ Instance

• **… Fast writes to local storage => flushed to EBS**
  - Writes go to fast local NVMe first
  - Then asynchronously flushed to durable EBS storage
  - Combines speed with durability

• **… Readers can be used for reads … allowing some read scaling**
  - Unlike Multi-AZ Instance, the 2 readers CAN serve read traffic
  - Provides read scaling capability
  - Writer handles all writes, readers handle reads

• **Replication is via transaction logs … more efficient**
  - Uses transaction log streaming instead of full data replication
  - More efficient and faster than traditional replication

• **Failover is faster ~35s + transaction log apply**
  - Much faster than Multi-AZ Instance (60-120s)
  - Average failover time is around 35 seconds
  - Plus time to apply any pending transaction logs

• **Writes are "committed" when 1 reader has confirmed**
  - Write is committed when the writer AND at least 1 of the 2 readers confirm
  - Provides durability while maintaining performance

### **🎯 Important Exam Points:**
- Multi-AZ **Cluster** vs Multi-AZ **Instance** - know the differences
- Cluster = **3 instances**, Instance = **2 instances**
- Cluster readers = **can serve reads** (Instance standby cannot)
- Cluster = **faster failover** (~35s vs 60-120s)
- Cluster = **better hardware** (Graviton + NVMe)
- Use Cluster when you need **read scaling + HA** in Multi-AZ

---

## **RDS – Backups – Cross-Region**

### Concepts Explained:

• **RDS can replicate backups to another region**
  - Automated backups and snapshots can be copied to a different AWS region
  - Provides disaster recovery capability for regional failures

• **… both snapshots and transaction logs**
  - Both snapshot backups and continuous transaction logs are replicated
  - Allows point-in-time recovery in the destination region

• **Charges apply for the cross-region data copy**
  - Data transfer costs for copying backups between regions
  - Standard cross-region data transfer pricing applies

• **… and the storage in the destination region**
  - You pay for S3 storage costs in the destination region
  - Charged for the size of backups stored

• **NOT DEFAULT … configured within automated backups**
  - Cross-region backup is not enabled by default
  - Must be manually configured in the RDS backup settings

### **🎯 Important Exam Points:**
- Cross-region backups = **disaster recovery** for region failure
- **NOT enabled by default** - must configure manually
- Includes **snapshots AND transaction logs**
- Incurs **data transfer + storage costs**

---

## **RDS – Restores**

### Concepts Explained:

• **Creates a NEW RDS Instance – new address**
  - Restoring from backup always creates a brand new RDS instance
  - The new instance has a different endpoint/DNS name
  - Original instance (if still exists) remains unchanged

• **Snapshots = single point in time, creation time**
  - Manual snapshots capture the database at a specific moment
  - Can only restore to that exact snapshot time
  - No flexibility for point-in-time recovery

• **Automated = any 5 minute point in time**
  - Automated backups allow point-in-time restore (PITR)
  - Can restore to any 5-minute interval within retention period
  - Uses snapshots + transaction logs

• **Backup is restored and transaction logs are 'replayed' to bring DB to desired point in time (GOOD RPO)**
  - Restore process: restore snapshot, then replay transaction logs
  - This achieves the specific point in time you selected
  - RPO (Recovery Point Objective) = how much data you can afford to lose
  - 5-minute granularity = maximum 5 minutes of data loss = good RPO

• **Restores aren't fast – Think about RTO (*RR's)**
  - Restore process can take considerable time (hours for large databases)
  - RTO (Recovery Time Objective) = how long recovery takes
  - For better RTO, consider Read Replicas that can be quickly promoted

### **🎯 Important Exam Points:**
- Restore = **NEW instance** with **NEW endpoint**
- Automated backups = **5-minute PITR**
- Manual snapshots = **single point in time** only
- Restores are **SLOW** (bad RTO) - use **Read Replicas** for faster recovery
- Retention period: **0-35 days** (0 = disabled)

---

## **RDS Read Replicas - Performance & RPO/RTO Improvements**

### **(read) Performance Improvements**

• **5x direct read-replicas per DB instance**
  - Each RDS instance can have up to 5 direct read replicas
  - Read replicas are separate instances that receive replicated data

• **Each providing an additional instance of read performance**
  - Each read replica can handle read queries
  - Distributes read load across multiple instances
  - Scales read capacity horizontally

• **Read-Replicas can have read-replicas – but lag starts to be a problem**
  - You can create read replicas of read replicas (cascading)
  - Creates a replication chain: Primary → RR1 → RR2
  - Increases replication lag at each level
  - Not recommended for production use

### **RPO/RTO Improvements**

• **Snapshots & Backups Improve RPO**
  - RPO = Recovery Point Objective (how much data loss is acceptable)
  - Regular snapshots minimize potential data loss
  - 5-minute PITR = 5-minute RPO maximum

• **RTO's are a problem**
  - RTO = Recovery Time Objective (how long to recover)
  - Restoring from backup takes hours = poor RTO

• **RR's offer near 0 RPO**
  - Read Replicas have near-real-time data replication
  - Replication lag is typically seconds
  - Minimal data loss if primary fails

• **RR's can be promoted quickly – low RTO**
  - Read Replica can be promoted to standalone DB in minutes
  - Much faster than restoring from backup
  - Dramatically improves RTO

• **Failure only – watch for data corruption**
  - Read Replicas help with infrastructure failure, not data corruption
  - If primary data is corrupted, replicas will also have corrupted data
  - Use backups/snapshots for data corruption recovery

• **Read only – until promoted**
  - Read Replicas are read-only while replicating
  - Once promoted, replication stops and it becomes a normal read-write instance
  - Cannot be re-attached to original primary after promotion

### **🎯 Important Exam Points:**
- Read Replicas = **scale READ performance**
- Up to **5 direct** read replicas per source
- Replication is **ASYNCHRONOUS** (some lag)
- Can be in **different regions** (cross-region read replicas)
- Can be **promoted** to standalone DB (breaks replication)
- Read Replica promotion = **fast RTO**
- Use Read Replicas for **DR** and **read scaling**
- **Does NOT help** with data corruption (use backups instead)

---

## **Amazon RDS Security**

### **Encryption at Rest**

• **SSL/TLS (in transit) is available for RDS, can be mandatory**
  - All RDS engines support encrypted connections
  - Can enforce SSL/TLS connections with parameter groups
  - Encrypts data in transit between clients and RDS

• **RDS supports EBS volume encryption – KMS**
  - RDS uses EBS volumes for storage
  - Can encrypt these volumes using AWS KMS
  - Encryption happens at the storage layer

• **Handled by HOST/EBS**
  - Encryption/decryption is handled by the host and EBS service
  - Transparent to the database engine
  - No performance impact

• **AWS or Customer Managed CMK generates data keys**
  - Can use AWS-managed keys or your own Customer Managed Keys (CMK)
  - CMK generates Data Encryption Keys (DEKs) for actual encryption
  - CMK never leaves KMS; only DEKs are used on hosts

• **Data keys used for encryption operations**
  - DEKs encrypt the actual data on EBS volumes
  - Unique DEK for each encrypted resource

• **Storage, Logs, Snapshots & replicas are encrypted**
  - When you enable encryption, everything is encrypted:
    - EBS volumes
    - Automated backups
    - Snapshots
    - Read Replicas
    - Transaction logs

• **…… encryption can't be removed**
  - Once an RDS instance is encrypted, it cannot be decrypted
  - Cannot disable encryption on an encrypted instance
  - Must create new unencrypted instance and migrate data

### **Transparent Data Encryption (TDE)**

• **RDS MSSQL and RDS Oracle Support TDE**
  - TDE is a database engine-level encryption feature
  - Only available for SQL Server and Oracle engines

• **… Transparent Data Encryption**
  - Encrypts data files at the database layer before writing to disk
  - Transparent to applications (no code changes needed)

• **Encryption handled within the DB engine**
  - Unlike KMS encryption (handled by host/EBS), TDE is done inside the database
  - Database engine encrypts/decrypts data
  - More control over encryption at DB level

### **🎯 Important Exam Points:**
- RDS encryption uses **KMS** (EBS-level)
- Encryption covers: **storage, backups, snapshots, replicas, logs**
- Encryption **cannot be removed** once enabled
- **Cannot encrypt existing unencrypted** instance (must create new + migrate)
- TDE = **Oracle & SQL Server only** (engine-level encryption)
- KMS encryption = **all engines** (host/EBS-level)
- **In-transit encryption** = SSL/TLS (enable via parameter groups)

---

## **Amazon RDS IAM Authentication**

### Concepts Explained:

• **Authorisation is controlled by the DB Engine**
  - IAM only handles authentication (proving who you are)
  - Authorization (what you can do) is managed by database permissions
  - IAM doesn't grant database-level permissions

• **Permissions are assigned to the local DB User**
  - Database permissions are managed within the database itself
  - GRANT/REVOKE statements in the database
  - IAM just maps to a DB user

• **IAM is NOT used to authorise, only for authentication**
  - IAM authenticates the user (verifies identity)
  - Database engine authorizes (checks permissions)

• **RDS Local DB Account configured to use AWS Authentication Token**
  - Create a database user account
  - Configure it to accept IAM authentication tokens instead of passwords
  - Uses AWSAuthenticationPlugin

• **generate-db-auth-token creates a token with 15 minute validity**
  - Use AWS CLI/SDK to generate a temporary authentication token
  - Token is valid for 15 minutes
  - No long-lived passwords stored

• **Policy attached to Users or Roles maps that IAM identity onto the local RDS user**
  - IAM policy grants `rds-db:connect` permission
  - Specifies which RDS instance and DB user
  - Policy maps IAM identity → database user

### **🎯 Important Exam Points:**
- IAM DB Auth = **authentication only**, NOT authorization
- Token is valid for **15 minutes**
- Requires IAM policy with **rds-db:connect** permission
- Supported engines: **MySQL, PostgreSQL, MariaDB** (NOT Oracle/SQL Server)
- Benefits: **no password management**, **IAM central auth**, **SSL required**
- Use IAM DB Auth for **EC2/Lambda** connecting to RDS

---

## **RDS – Custom**

### Concepts Explained:

• **Fills the gap between RDS and EC2 running a DB Engine**
  - Standard RDS = fully managed, no OS access
  - EC2 + database = full control, full responsibility
  - RDS Custom = middle ground

• **RDS is fully managed – OS/Engine access is limited**
  - Standard RDS doesn't allow OS-level customization
  - Some applications need custom OS configurations or agents

• **DB on EC2 is self managed – but has overhead**
  - Running DB on EC2 gives full control
  - But you manage everything: patching, backups, HA, etc.

• **Currently works for MS SQL and Oracle**
  - RDS Custom only available for SQL Server and Oracle
  - Not available for MySQL, PostgreSQL, MariaDB

• **Can connect using SSH, RDP, Session Manager**
  - Full OS access via SSH (Linux) or RDP (Windows)
  - Can use Systems Manager Session Manager
  - Install custom agents, modify OS settings

• **RDS Custom Database Automation**
  - AWS still provides some automation (backups, patching)
  - But you control when and how it happens

• **… pause to customize (so there are no disruptions)**
  - Pause automation when making custom changes
  - Prevents AWS automation from interfering with your changes

• **… Resume (full automation) for normal production usage**
  - Resume automation after customization complete
  - Returns to managed state with AWS handling routine tasks

### **🎯 Important Exam Points:**
- RDS Custom = **OS access** + **some AWS management**
- Only for **Oracle** and **SQL Server**
- Use case: need **custom OS config**, agents, or **DB engine customization**
- If you need **full** control → use **EC2 + database**
- If you don't need OS access → use **standard RDS**
- RDS Custom = **middle ground** between RDS and EC2

---

## **Aurora Storage Architecture**

### Concepts Explained:

• **All SSD Based – high IOPS, low latency**
  - Aurora only uses SSD storage (no HDD option)
  - Provides consistently high performance
  - Low latency for database operations

• **Storage is billed based on what's used**
  - You don't provision storage capacity upfront
  - Automatically scales as data grows
  - Pay only for actual storage consumed

• **High water mark – billed for the most used**
  - Billing is based on the highest storage used
  - If you delete data, you still pay for peak usage
  - Storage doesn't automatically shrink

• **Storage which is freed up can be re-used**
  - Deleted data space can be used for new data
  - Doesn't reduce billing but avoids growth

• **Replicas can be added and removed without requiring storage provisioning**
  - Aurora separates compute and storage
  - All replicas share the same storage layer
  - Adding replicas doesn't duplicate storage

### **🎯 Important Exam Points:**
- Aurora storage = **auto-scaling** (10GB to 128TB)
- Billed on **high water mark** (peak usage)
- **Shared storage** across all instances in cluster
- **6 copies** across 3 AZs (2 copies per AZ)
- Can survive loss of **2 copies** (write available)
- Can survive loss of **3 copies** (read available)
- Storage is **self-healing** (corrupted blocks automatically repaired)

---

## **Aurora Cost**

### Concepts Explained:

• **No free-tier option**
  - Aurora is not included in AWS free tier
  - Must pay from day one

• **Aurora doesn't support Micro Instances**
  - Minimum instance size is larger than t2.micro/t3.micro
  - Cannot use the smallest/cheapest instance types

• **Beyond RDS singleAZ (micro) Aurora offers better value**
  - For production workloads, Aurora can be more cost-effective than RDS
  - Better performance per dollar at scale
  - But more expensive than minimal RDS configurations

• **Compute – hourly charge, per second, 10 minute minimum**
  - Charged for Aurora instances (compute)
  - Per-second billing after 10-minute minimum
  - Similar to EC2 pricing model

• **Storage – GB-Month consumed, IO cost per request**
  - Pay for storage used (GB-month)
  - Also pay for I/O operations (reads/writes)
  - I/O costs can add up for high-throughput workloads

• **100% DB Size in backups are included**
  - Automated backups up to 100% of database size are free
  - Manual snapshots beyond that are charged
  - Generous compared to RDS backup storage

### **🎯 Important Exam Points:**
- Aurora = **no free tier**, **no micro instances**
- Aurora pricing = **compute + storage + I/O**
- **I/O charges** can be significant (use Aurora I/O-Optimized for high I/O)
- Aurora is **more expensive** than minimal RDS but **better value** at scale
- Consider Aurora for: **high performance**, **high availability**, **read scaling**

---

## **Aurora Restore, Clone & Backtrack**

### Concepts Explained:

• **Backups in Aurora work in the same way as RDS**
  - Automated backups with retention period (1-35 days)
  - Manual snapshots
  - Continuous backup to S3

• **Restores create a new cluster**
  - Like RDS, restoring creates a new Aurora cluster
  - New endpoint, doesn't affect source cluster

• **Backtrack can be used which allow in-place rewinds to a previous point in time**
  - Backtrack is unique to Aurora
  - Rewinds database to previous state WITHOUT creating new cluster
  - In-place operation - same cluster, same endpoint
  - Very fast (minutes, not hours)

• **Fast clones make a new database MUCH faster than copying all the data – copy-on-write**
  - Aurora cloning uses copy-on-write technology
  - Creates a new cluster instantly (minutes)
  - New cluster shares storage with source initially
  - As data changes, only differences are stored
  - Perfect for dev/test environments

### **🎯 Important Exam Points:**
- **Backtrack** = in-place rewind (no new cluster, very fast)
- Backtrack must be **enabled** when creating cluster
- Backtrack window: **up to 72 hours**
- **Fast Clone** = copy-on-write (instant cluster creation)
- Use Fast Clone for **dev/test** environments
- Clone is **cost-effective** (shares storage initially)

---

## **Aurora Serverless Concepts**

### Concepts Explained:

• **Scalable – ACU – Aurora Capacity Units**
  - ACU = Aurora Capacity Unit (combination of CPU, memory, networking)
  - Capacity measured in ACUs instead of instance types

• **Aurora Serverless cluster has a MIN & MAX ACU**
  - Define minimum and maximum capacity range
  - Example: MIN=2 ACU, MAX=32 ACU
  - Cluster scales within this range

• **Cluster adjusts based on load**
  - Automatically scales up when load increases
  - Scales down when load decreases
  - Seamless scaling without downtime

• **Can go to 0 and be paused**
  - Can scale to zero ACUs when no activity
  - Database is paused - not charged for compute
  - Only pay for storage when paused

• **Consumption billing per-second basis**
  - Pay only for the ACU-seconds consumed
  - No charges when paused (except storage)
  - Very cost-effective for intermittent workloads

• **Same resilience as Aurora (6 copies across AZs)**
  - Same underlying storage architecture as regular Aurora
  - Same high availability and durability

### **🎯 Important Exam Points:**
- Aurora Serverless = **automatic scaling** based on load
- Can **pause** when inactive (no compute charges)
- Perfect for: **intermittent**, **unpredictable**, **dev/test** workloads
- Versions: **v1** (older, limited features), **v2** (current, better scaling)
- v2 = **instant scaling**, v1 = scaling can take time
- **Shared VPC** access via Data API (no VPC needed for client)

---

## **Aurora Serverless – Use Cases**

### Concepts Explained:

• **Infrequently used applications**
  - Applications used occasionally (few times per day/week)
  - Can pause between uses to save costs
  - Example: internal admin tools

• **New applications**
  - Uncertain capacity requirements
  - Let it scale based on actual usage
  - Avoid over-provisioning

• **Variable workloads**
  - Load varies significantly over time
  - Peaks and valleys in usage
  - Example: business apps with heavy daytime use, light nighttime use

• **Unpredictable workloads**
  - Cannot forecast load patterns
  - Serverless handles sudden spikes automatically

• **Development and test databases**
  - Used only during work hours
  - Can pause overnight and weekends
  - Significant cost savings

• **Multi-tenant applications**
  - Each tenant's load varies
  - Combined load may be unpredictable
  - Serverless handles aggregate variation

### **🎯 Important Exam Points:**
- Serverless = **variable/unpredictable** workloads
- **Cost optimization** for intermittent use
- **NOT for**: steady 24/7 workloads (regular Aurora is better)
- **NOT for**: high-performance requirements (use provisioned instances)

---

## **Aurora Global Database**

### Concepts Explained:

• **Cross-Region DR and BC**
  - DR = Disaster Recovery
  - BC = Business Continuity
  - Protects against entire region failure

• **Global Read Scaling – low latency performance improvements**
  - Replicas in multiple regions serve local read traffic
  - Reduces latency for globally distributed users
  - Each region can have up to 16 read replicas

• **~1s or less replication between regions**
  - Typical replication lag is under 1 second
  - Very fast cross-region replication
  - Uses dedicated Aurora infrastructure

• **No impact on DB performance**
  - Replication happens at storage layer
  - Doesn't affect primary region performance
  - Efficient and optimized

• **Secondary regions can have 16 replicas**
  - Each secondary region can have up to 16 read replicas
  - Massive read scaling capability

• **… Can be promoted to R/W**
  - Secondary region can be promoted to read-write
  - Becomes the new primary in disaster scenario
  - Failover in under 1 minute (RTO < 1 minute)

• **Currently MAX 5 secondary regions…**
  - Can have 1 primary + up to 5 secondary regions
  - Total of 6 regions maximum

### **🎯 Important Exam Points:**
- Aurora Global = **cross-region** DR and **read scaling**
- Replication lag = **< 1 second** (typical)
- Secondary region promotion = **< 1 minute** RTO
- Up to **5 secondary regions**
- Each secondary = up to **16 read replicas**
- Use for: **global apps**, **disaster recovery**, **low-latency reads**

---

## **Aurora Multi-Master**

### Concepts Explained:

• **Default Aurora mode is Single-Master**
  - Normal Aurora has one writer (master)
  - Multiple read replicas possible
  - Only the master can accept writes

• **One R/W and 0+ Read Only Replicas**
  - In single-master: 1 instance handles writes
  - Other instances are read-only replicas

• **Cluster Endpoint is used to write, read endpoint is used for load balanced reads**
  - Cluster endpoint points to the writer
  - Reader endpoint load balances across read replicas

• **Failover takes time – replica promoted to R/W**
  - If writer fails, a replica is promoted
  - Promotion takes time (seconds to minutes)
  - Brief downtime during failover

• **In Multi-Master mode all instances are R/W**
  - ALL instances can accept writes
  - No read-only replicas in multi-master
  - True active-active configuration

### **🎯 Important Exam Points:**
- Multi-Master = **all instances are read-write**
- Use case: **continuous write availability**
- If one writer fails, other writers continue immediately
- **No failover delay** (writes always available)
- Application must handle **conflict resolution**
- Multi-Master = **high write availability**, NOT horizontal write scaling
- Currently supports **MySQL only** (not PostgreSQL)

---

## **RDS – Proxy – When?**

### Concepts Explained:

• **Too many connections errors…**
  - Applications opening too many database connections
  - Database running out of available connections
  - Connection limit exceeded

• **DB Instances using T2/T3 (i.e smaller/burst) instances**
  - Smaller instances have limited memory and connection capacity
  - T-series instances can be overwhelmed by connections

• **AWS Lambda** (implicit from context)
  - Lambda functions create new DB connections for each invocation
  - High concurrency = hundreds/thousands of connections
  - Quickly exhausts database connection pool

### **What RDS Proxy Does:**
- **Connection pooling**: reuses database connections
- **Reduces** number of active connections to database
- **Multiplexes** many application connections → fewer DB connections
- **IAM authentication** for database access
- **Enforces** SSL/TLS connections
- **Reduces** failover time by 66% (maintains connections during failover)

### **🎯 Important Exam Points:**
- RDS Proxy = **connection pooling** for RDS
- Perfect for: **Lambda**, **high-connection apps**, **small instances**
- **Reduces** database CPU/memory usage
- Supports: **MySQL, PostgreSQL, MariaDB, SQL Server**
- **Maintains connections** during failover (improved availability)
- Fully **serverless** and **auto-scaling**
- Access via **VPC** only (not public)

---

## **Elastic Load Balancer (ELB) – Key Concepts**

### Concepts Explained:

• **Each ELB is a DNS A record pointing to ELB nodes**
  - ELB is accessed via DNS name (not IP address)
  - DNS resolves to multiple ELB nodes
  - Nodes are the actual load balancers

• **Nodes (one subnet per AZ) scale with load**
  - At least one ELB node per enabled AZ
  - Nodes automatically scale based on traffic
  - More nodes added as load increases

• **Internet-facing → nodes have public IPv4 IPs**
  - Internet-facing ELB nodes are in public subnets
  - Have public IPs to receive internet traffic
  - Can route to private or public targets

• **Internal → private IPs only**
  - Internal ELB nodes are in private subnets
  - Only have private IPs
  - Only accessible within VPC or via VPN/Direct Connect

• **EC2 instances do not need public IPs to work with a load balancer**
  - Targets behind ELB can be in private subnets
  - ELB handles internet connectivity
  - Targets only need private IPs

• **Listener configuration controls WHAT the LB does**
  - Listeners define ports and protocols ELB listens on
  - Rules determine how to route traffic
  - Can have multiple listeners (e.g., port 80 and 443)

• **8+ free IPs per subnet, and /27 or larger subnet required for scaling**
  - ELB nodes need IP addresses in each subnet
  - AWS recommends /27 (32 IPs) or larger
  - Minimum 8 free IPs required
  - More IPs needed as ELB scales

### **🎯 Important Exam Points:**
- ELB is **regional** (not global) - operates within one region
- ELB has **multiple nodes** across AZs
- Internet-facing ELB = **public subnets**, Internal ELB = **private subnets**
- Subnet needs **/27 or larger** with **8+ free IPs**
- ELB DNS name **never changes** (but IPs can)
- **Never** use ELB node IPs directly (use DNS name)
- ELB nodes **scale automatically** with load

---

## **Cross-Zone Load Balancing**

### Concepts Explained:

• **Traffic is distributed evenly across all registered targets in all AZs**
  - Without cross-zone: traffic is balanced across AZs only
  - With cross-zone: traffic is balanced across ALL targets regardless of AZ
  - Ensures even distribution

• **Each node receives 100% ÷ number of nodes (e.g. 50% each in a 2-node setup)**
  - Without cross-zone: each ELB node gets 50% of traffic
  - That traffic only goes to targets in the same AZ
  - Can create imbalance if AZs have different target counts

• **Prevents uneven load when AZs have different target counts**
  - Example: AZ-A has 5 instances, AZ-B has 1 instance
  - Without cross-zone: AZ-B instance gets 50% of traffic (overloaded)
  - With cross-zone: all 6 instances get 16.67% each (balanced)

### **🎯 Important Exam Points:**
- **ALB**: Cross-zone enabled by **default** (always on, no charge)
- **NLB**: Cross-zone **disabled** by default (can enable, **charges apply**)
- **CLB**: Cross-zone **disabled** by default (can enable, no charge)
- Enable cross-zone when **targets are unevenly distributed** across AZs

---

## **Application Load Balancer (ALB)**

### Concepts Explained:

• **Layer 7 load balancer – listens on HTTP and/or HTTPS**
  - Operates at Application Layer (Layer 7 of OSI model)
  - Understands HTTP/HTTPS protocol
  - Can inspect HTTP headers, paths, methods

• **No other Layer 7 protocols (SMTP, SSH, gaming, etc.)**
  - Only supports HTTP and HTTPS
  - Cannot load balance SMTP, FTP, SSH, or custom protocols

• **No TCP/UDP/TLS listeners**
  - Cannot handle raw TCP or UDP traffic
  - TLS is terminated, not passed through

• **Uses L7 attributes: content type, cookies, headers, user location, app behaviour**
  - Can route based on:
    - URL path (/api/* vs /images/*)
    - Host header (api.example.com vs www.example.com)
    - HTTP method (GET, POST, etc.)
    - Query strings
    - Source IP
    - HTTP headers

• **SSL/TLS is always terminated on the ALB (no unbroken SSL)**
  - ALB decrypts incoming HTTPS traffic
  - Makes a new connection to targets
  - Cannot do SSL passthrough

• **A new connection is made from ALB to the application**
  - Two separate connections: client→ALB, ALB→target
  - Connection state is not preserved

• **ALBs must have SSL certificates if HTTPS is used**
  - Install certificates on ALB (via ACM or IAM)
  - ALB handles SSL/TLS termination

• **ALBs are slower than NLBs (more layers in the stack)**
  - Layer 7 processing adds latency
  - More overhead than Layer 4

• **Health checks evaluate application health (Layer 7)**
  - Can check specific HTTP paths (e.g., /health)
  - Verifies HTTP response codes
  - More intelligent than Layer 4 health checks

### **🎯 Important Exam Points:**
- ALB = **HTTP/HTTPS only** (Layer 7)
- ALB **terminates SSL** (cannot passthrough)
- Use ALB for **web applications**
- ALB = **content-based routing** (path, host, headers)
- ALB targets: **EC2 instances, IP addresses, Lambda functions, containers**
- ALB supports: **WebSocket**, **HTTP/2**, **gRPC**
- ALB **slower** than NLB but **smarter** routing

---

## **Application Load Balancer (ALB) – Rules**

### Concepts Explained:

• **Rules direct connections that arrive at a listener**
  - Listeners receive traffic (e.g., port 80 or 443)
  - Rules determine how to handle that traffic
  - Each listener can have multiple rules

• **Rules are processed in priority order (top → bottom)**
  - Rules have priority numbers (lower number = higher priority)
  - First matching rule is applied
  - Remaining rules are ignored

• **Default rule = catch-all (always exists)**
  - Every listener has a default rule
  - Lowest priority (evaluated last)
  - Catches traffic that doesn't match other rules

### **Rule Conditions:**

• **host-header**
  - Route based on Host header (domain name)
  - Example: api.example.com → Target Group A, www.example.com → Target Group B

• **http-header**
  - Route based on HTTP headers
  - Example: User-Agent contains "mobile" → mobile target group

• **http-request-method**
  - Route based on HTTP method
  - Example: POST requests → write target group, GET → read target group

• **path-pattern**
  - Route based on URL path
  - Example: /api/* → API servers, /images/* → image servers

• **query-string**
  - Route based on query parameters
  - Example: ?version=2 → v2 target group

• **source-ip**
  - Route based on client IP address
  - Example: 10.0.0.0/8 → internal target group

### **Actions:**

• **forward (to target group)**
  - Send traffic to one or more target groups
  - Can distribute across multiple target groups with weights

• **redirect (HTTP → HTTPS etc.)**
  - Return HTTP redirect response to client
  - Common: redirect HTTP to HTTPS

• **fixed-response (static reply)**
  - Return a static HTTP response
  - Useful for maintenance pages or custom error messages

• **authenticate-oidc**
  - Authenticate users via OIDC provider (e.g., Google, Facebook)
  - ALB handles authentication before forwarding

• **authenticate-cognito**
  - Authenticate users via Amazon Cognito
  - Built-in integration with AWS Cognito User Pools

### **🎯 Important Exam Points:**
- Rules enable **content-based routing**
- Rule conditions can be **combined** (AND logic)
- Use **path-based** routing for microservices
- Use **host-based** routing for multiple domains
- **Authenticate** action enables built-in authentication
- Rules processed in **priority order** (lowest number first)

---

## **Network Load Balancer (NLB)**

### Concepts Explained:

• **Layer 4 load balancer**
  - Operates at Transport Layer (Layer 4 of OSI model)
  - Deals with TCP/UDP connections
  - No understanding of application protocols

• **Supports TCP, TLS, UDP, TCP_UDP**
  - Can handle any TCP or UDP traffic
  - TLS listener for TLS termination (but can also passthrough)

• **No understanding of HTTP/HTTPS**
  - Does not inspect HTTP headers, paths, or methods
  - Treats HTTP as generic TCP traffic

• **No headers, cookies, or session stickiness**
  - Cannot read or modify HTTP headers
  - Stickiness only via source IP (not cookies)

• **Extremely fast (millions of rps, ~25% ALB latency)**
  - Much lower latency than ALB
  - Can handle millions of requests per second
  - Roughly 4x faster than ALB

### **Use cases:**

• **SMTP, SSH, game servers, financial apps**
  - Any non-HTTP protocol
  - Low-latency requirements
  - High throughput needs

• **Anything NOT HTTP/S**
  - If it's not web traffic, likely needs NLB

### **Health checks:**

• **Only ICMP / TCP handshake**
  - Health check verifies TCP connection succeeds
  - Or ICMP ping response
  - Cannot check HTTP response codes

• **Not application-aware**
  - Cannot verify application is functioning correctly
  - Only verifies network connectivity

### **Key features:**

• **Can use static IPs (great for whitelisting)**
  - NLB nodes get static Elastic IPs
  - Useful for firewall rules and whitelisting
  - Predictable IP addresses

• **Supports unbroken encryption (TLS passes through)**
  - TLS can pass through NLB without termination
  - End-to-end encryption maintained
  - ALB cannot do this

• **Used with PrivateLink to expose services to other VPCs**
  - NLB is the only ELB type that works with VPC PrivateLink
  - Enables service sharing across VPCs/accounts

### **🎯 Important Exam Points:**
- NLB = **Layer 4** (TCP/UDP/TLS)
- **Fastest** ELB option (lowest latency)
- Use for **non-HTTP** protocols
- Supports **static IPs** (1 per AZ)
- Supports **unbroken TLS** (passthrough)
- **Required** for PrivateLink
- NLB targets: **EC2 instances, IP addresses, ALB** (yes, can chain!)
- Health checks are **basic** (TCP/ICMP only)

---

## **ALB vs NLB – Exam Logic**

### Decision Framework:

**Choose NLB when you need:**

• **Unbroken encryption**
  - TLS must pass through without termination
  - End-to-end encryption required
  - ALB always terminates SSL

• **Static IPs**
  - Need predictable IP addresses
  - Firewall whitelisting requirements
  - ALB IPs change

• **Fastest performance (millions rps)**
  - Extreme low latency needed
  - Millions of requests per second
  - Gaming, trading, high-throughput apps

• **Non-HTTP/S protocols**
  - SMTP, SSH, FTP, custom protocols
  - Anything other than HTTP/HTTPS

• **PrivateLink**
  - Sharing services via VPC PrivateLink
  - Only NLB supports PrivateLink

### Otherwise → ALB

- Web applications (HTTP/HTTPS)
- Need content-based routing
- Need Layer 7 features (authentication, redirects)
- WebSocket or HTTP/2 support needed

### **🎯 Important Exam Points:**
- **Default choice for web apps = ALB**
- **Non-web or need NLB-specific features = NLB**
- Both support **target health checks** and **multiple AZs**
- Both are **highly available** and **scale automatically**

---

## **Launch Configurations (LC) & Launch Templates (LT)**

### Concepts Explained:

• **Define EC2 configuration in advance**
  - Pre-define all EC2 instance settings
  - Used by Auto Scaling Groups to launch instances
  - Ensures consistent configuration

• **Include:**
  - **AMI**: Which OS/software image to use
  - **Instance type**: t3.micro, m5.large, etc.
  - **Storage**: EBS volumes (size, type, encryption)
  - **Key pair**: SSH key for access
  - **Networking & Security Groups**: Network configuration
  - **User data**: Bootstrap scripts
  - **IAM role**: Instance permissions

### **Important:**

• **Both are immutable (can't edit once created)**
  - Cannot modify a launch configuration or template after creation
  - Must create new version if changes needed

• **Launch Templates support versions**
  - Can create multiple versions of a template
  - Specify which version ASG should use
  - Easy to test changes and rollback

• **LT supports newer features:**
  - **T2/T3 Unlimited**: Burst beyond baseline indefinitely
  - **Placement groups**: Control instance placement
  - **Capacity reservations**: Reserve capacity in advance
  - **Elastic Graphics**: GPU acceleration

• **Launch Templates are preferred**
  - AWS recommends Launch Templates over Launch Configurations
  - More flexible and supports more features
  - Launch Configurations are legacy (still supported but outdated)

### **🎯 Important Exam Points:**
- **Launch Template = NEWER and BETTER** (use this)
- Launch Configuration = **legacy** (avoid for new implementations)
- Launch Templates support **versions** (LC does not)
- Both are **immutable** (cannot edit, must create new)
- Launch Templates support **mixed instance types** (LC does not)
- Launch Templates support **spot + on-demand** mix (LC does not)

---

## **Auto Scaling Groups (ASG)**

### Concepts Explained:

• **Provide automatic scaling + self-healing**
  - **Scaling**: Add/remove instances based on demand
  - **Self-healing**: Replace unhealthy instances automatically

• **Use Launch Templates or Launch Configurations**
  - Define WHAT to launch (instance configuration)
  - ASG handles WHEN and WHERE to launch

• **Defined by:**
  - **Minimum**: Minimum number of instances (e.g., 1)
  - **Desired**: Target number of instances (e.g., 2)
  - **Maximum**: Maximum number of instances (e.g., 4)

• **ASG keeps instances at desired capacity**
  - Continuously monitors instance count
  - Launches new instances if below desired
  - Terminates excess instances if above desired

• **Launches new instances**
  - Uses Launch Template/Configuration
  - Distributes across multiple AZs for HA

• **Terminates excess or unhealthy ones**
  - Removes instances that fail health checks
  - Terminates oldest instances first (by default)

• **Scaling is controlled via scaling policies**
  - Policies change the desired capacity based on conditions
  - Types: manual, scheduled, dynamic

### **🎯 Important Exam Points:**
- ASG = **WHEN and WHERE** to launch instances
- Launch Template = **WHAT** to launch
- ASG is **FREE** (only pay for EC2 instances)
- ASG **integrates with ELB** (automatic registration)
- ASG spreads instances across **multiple AZs**
- Minimum can be **0** (all instances can be terminated)
- **Health checks**: EC2 status or ELB health
- Replacement instance launched in **same AZ** as terminated one (usually)

---

## **Scaling Policies**

### **Manual Scaling:**

• **Manually adjust desired capacity**
  - Operator manually changes desired count
  - No automation involved
  - Used for planned events

### **Scheduled Scaling:**

• **Make changes at specific times (sales, business hours)**
  - Pre-define scaling actions for specific dates/times
  - Example: Scale up at 9 AM, scale down at 6 PM
  - Good for predictable patterns

### **Dynamic Scaling:**

• **Reacts to metrics**
  - Automatically adjusts based on CloudWatch metrics
  - Most common and flexible approach

### **Types:**

• **Simple**
  - Single scaling adjustment
  - Example: "If CPU > 50% → add 1 instance"
  - Waits for cooldown period before next action
  - Basic and easy to understand

• **Step Scaling**
  - Multiple scaling steps based on metric breach severity
  - Example: 
    - CPU 50-60% → add 1 instance
    - CPU 60-80% → add 2 instances
    - CPU > 80% → add 3 instances
  - More granular than simple scaling

• **Target Tracking**
  - Define a target value for a metric
  - Example: "Keep average CPU at 40%"
  - ASG automatically adjusts to maintain target
  - AWS calculates when/how much to scale
  - Easiest to configure and most commonly used

• **Cooldown periods prevent rapid scaling loops**
  - Delay after scaling action before next action
  - Prevents thrashing (rapid scale up/down)
  - Default: 300 seconds (5 minutes)

### **🎯 Important Exam Points:**
- **Target Tracking** = EASIEST and MOST COMMON
- Use **Step Scaling** for complex scenarios
- **Simple Scaling** = legacy (target tracking is better)
- **Scheduled Scaling** = predictable patterns (business hours)
- Cooldowns prevent **scaling storms**
- **Shorter cooldowns** = faster response but more instance churn
- **CloudWatch metrics** drive dynamic scaling

---

## **Scaling Processes (ASG Internals)**

### Concepts Explained:

• **Launch / Terminate – can suspend & resume**
  - Controls ability to launch or terminate instances
  - Suspend to prevent ASG from taking scaling actions
  - Useful during maintenance or troubleshooting

• **AddToLoadBalancer – attach instance on launch**
  - Automatically registers new instances with ELB
  - Suspend if you want to manually control ELB registration

• **AlarmNotification – integrates with CloudWatch**
  - Enables CloudWatch alarms to trigger scaling
  - Suspend to ignore alarm-based scaling

• **AZRebalance – spreads instances evenly across AZs**
  - Maintains even distribution across AZs
  - May terminate and relaunch instances to achieve balance
  - Suspend if you don't want rebalancing

• **HealthCheck – enable/disable health checks**
  - Controls health check monitoring
  - Suspend to prevent replacement of unhealthy instances

• **ReplaceUnhealthy – terminate & replace bad instances**
  - Automatically replaces failed instances
  - Suspend to keep unhealthy instances running (for debugging)

• **ScheduledActions – turn scaling on/off**
  - Controls scheduled scaling actions
  - Suspend to prevent scheduled scaling

• **Standby – instance stays running but removed from service**
  - Puts instance in standby state
  - Still running but not serving traffic
  - Not replaced by ASG
  - Useful for maintenance without termination

### **🎯 Important Exam Points:**
- **Suspend processes** for maintenance or troubleshooting
- **Standby state** = instance alive but out of service
- **AZRebalance** can cause instances to be replaced
- You can suspend **individual processes** (not all or nothing)
- Suspended processes remain suspended until **manually resumed**

---

## **Auto Scaling Groups - Final Exam Points**

• **Auto Scaling Groups are FREE**
  - No charge for the ASG itself
  - Only pay for EC2 instances launched

• **You only pay for EC2 instances created**
  - Standard EC2 pricing applies
  - On-demand, reserved, or spot pricing

• **Use cooldowns to avoid scaling storms**
  - Cooldown period prevents rapid scaling cycles
  - Balance responsiveness with stability

• **Prefer many small instances (better granularity)**
  - 10 small instances > 2 large instances
  - More granular scaling (add/remove 10% vs 50%)
  - Better fault tolerance

• **Use ASG with ALB for elasticity**
  - ALB distributes traffic across instances
  - ASG adjusts instance count
  - Perfect combination for scalable web apps

• **ASG = WHEN & WHERE**
  - When to scale (policies, schedule)
  - Where to launch (AZs, subnets)

• **Launch Template = WHAT**
  - What to launch (AMI, instance type, etc.)

### **🎯 Important Exam Points:**
- ASG + ALB = **scalable, highly available web tier**
- **Health check grace period** = wait before first health check
- ASG respects **Availability Zone balance**
- **Termination policy**: oldest launch config first (default)
- Can use **multiple scaling policies** simultaneously
- ASG works with **on-demand, reserved, and spot instances**

---

## **ASG Lifecycle Hooks**

### Concepts Explained:

• **Allow custom actions during ASG events**
  - Hook into ASG lifecycle to run custom code
  - Pause instance launch or termination process

• **Triggered on:**
  - **Instance launch**: Before instance goes into service
  - **Instance terminate**: Before instance is terminated

• **Instance is paused in the lifecycle flow**
  - Instance is in a wait state (Pending:Wait or Terminating:Wait)
  - Not yet in service (launch) or not yet terminated (terminate)

• **Instance waits:**
  - **Until timeout**: Default timeout (1 hour)
  - Then **CONTINUE** (complete action) or **ABANDON** (rollback)

• **Or until you explicitly resume using CompleteLifecycleAction**
  - API call to tell ASG to continue
  - Can be called by Lambda, script, or other automation

• **Common integrations:**
  - **EventBridge**: Trigger Lambda or other actions
  - **SNS**: Send notifications for manual intervention

### **Typical uses:**

• **Config management**
  - Install software, configure settings
  - Warm up application before serving traffic

• **Register/deregister with external systems**
  - Update external load balancer
  - Register with monitoring systems
  - Update DNS records

• **Drain connections before termination**
  - Gracefully shutdown application
  - Complete in-flight requests
  - Clean up resources

### **🎯 Important Exam Points:**
- Lifecycle hooks **pause** instance launch/termination
- Default timeout = **1 hour** (can configure)
- Use with **EventBridge** for automated actions
- Use with **SNS** for notifications
- Common use: **warm up** instances before receiving traffic
- Common use: **drain connections** before termination
- Instance in **Pending:Wait** or **Terminating:Wait** state

---

## **ASG Health Checks**

### **Types:**

• **EC2 (default)**
  - Basic infrastructure health checks
  - Only checks if instance is running

• **ELB (optional, recommended)**
  - Application-level health checks
  - Checks if application is responding correctly

• **Custom**
  - External system determines health
  - You control health status via API

### **EC2 Health:**

**Instance is UNHEALTHY if:**

• **Stopping / Stopped**
  - Instance is not running

• **Terminated / Shutting down**
  - Instance is being or has been terminated

• **Impaired**
  - System status check failed

• **Not 2/2 status checks**
  - System status check OR instance status check failed
  - Must pass both to be healthy

### **ELB Health:**

• **Instance must be running**
  - Same as EC2 health

• **Must pass ELB health checks**
  - ELB performs HTTP/HTTPS health checks
  - Checks specific endpoint (e.g., /health)

• **More application-aware (Layer 7)**
  - Can detect application failures
  - Better than basic EC2 checks

### **Custom Health:**

• **External system marks instance healthy/unhealthy**
  - You call SetInstanceHealth API
  - Based on your own criteria
  - Full control over health status

### **🎯 Important Exam Points:**
- **EC2 health checks** = basic (is instance running?)
- **ELB health checks** = better (is application working?)
- **Always use ELB health checks** with load balanced applications
- ASG **terminates and replaces** unhealthy instances
- Health check type is **configured on ASG**, not Launch Template

---

## **Health Check Grace Period**

### Concepts Explained:

• **Default: 300 seconds**
  - 5 minutes by default
  - Can be configured to different value

• **Delay before health checks start**
  - Health checks don't start immediately after launch
  - Grace period timer starts when instance enters InService state

• **Prevents premature termination**
  - Gives instance time to initialize
  - Avoids terminating instance that's still booting

• **Allows:**
  - **OS boot**: Operating system startup
  - **Bootstrap scripts**: User data execution
  - **App startup**: Application initialization, warm-up

### **🎯 Important Exam Points:**
- Grace period = **time before first health check**
- Default = **300 seconds** (5 minutes)
- **Set grace period** > time for instance to fully start
- If instance fails health check before grace period → **NOT terminated**
- After grace period expires → health checks start normally
- Too short = premature termination, Too long = slow failure detection

---

## **SSL / TLS Handling with Load Balancers**

### **Bridging:**

• **Client → HTTPS → ELB**
  - Client connects to ELB via HTTPS

• **ELB → HTTPS → instances**
  - ELB connects to instances via HTTPS

• **ELB terminates SSL, then re-encrypts**
  - ELB decrypts incoming traffic
  - Inspects/processes if needed
  - Re-encrypts before sending to instances

• **Instances need certificates**
  - Each instance must have SSL certificate
  - Certificate management on instances required

• **Higher compute cost**
  - ELB and instances both do encryption/decryption
  - More CPU usage on both sides

### **Pass-Through:**

• **Client → TLS → NLB**
  - Client connects to NLB via TLS

• **No decryption on LB**
  - NLB does NOT terminate TLS
  - Traffic passes through encrypted

• **Encryption stays intact**
  - End-to-end encryption maintained
  - NLB never sees decrypted data

• **Instances manage certificates**
  - Each instance needs SSL certificate
  - All certificate management on instances

• **No certificate exposure to AWS**
  - AWS never has access to certificate private keys
  - Maximum security for sensitive applications

• **Used when unbroken encryption is required**
  - Compliance requirements
  - Maximum security scenarios
  - Only possible with NLB

### **Offload (Most Common):**

• **Client → HTTPS → ELB**
  - Client connects to ELB via HTTPS

• **ELB terminates SSL**
  - ELB decrypts incoming traffic

• **ELB → HTTP → instances**
  - ELB connects to instances via plain HTTP
  - No encryption between ELB and instances

• **Instances do NOT need certs**
  - No certificate management on instances
  - Simpler instance configuration

• **Lower CPU usage on instances**
  - Instances don't do encryption/decryption
  - More CPU available for application processing

### **Summary:**

• **ALB = SSL offload**
  - ALB always terminates SSL
  - Cannot do passthrough
  - Most common configuration

• **NLB = SSL pass-through**
  - NLB can do passthrough
  - Maintains end-to-end encryption
  - Can also do termination if needed

### **🎯 Important Exam Points:**
- **Offload** = most common (ALB)
- **Passthrough** = end-to-end encryption (NLB only)
- **Bridging** = uncommon (high compute cost)
- ALB **cannot** do passthrough (always terminates)
- NLB **can** do passthrough or termination
- Offload = **easiest** (certificates on ELB only)
- Passthrough = **most secure** (AWS never sees decrypted data)

---

## **Connection Stickiness (Session Affinity)**

### Concepts Explained:

• **Stickiness uses a cookie**
  - Load balancer sets a cookie in client browser
  - Cookie contains information about which backend instance to use

• **Cookie binds client → same backend instance**
  - All requests from that client go to same instance
  - As long as cookie is valid and instance is healthy

• **Duration: 1 second → 7 days**
  - Configure how long stickiness lasts
  - Cookie expires after this duration

### **Without stickiness:**

• **Requests distributed across all healthy instances**
  - Each request can go to different instance
  - Round-robin or other algorithm

• **Can cause:**
  - **User logouts**: Session data on different instance
  - **Lost shopping carts**: Cart data not shared between instances
  - **Session issues**: Any session-dependent functionality breaks

### **Best practice:**

• **Prefer stateless apps**
  - Store session data externally (DynamoDB, ElastiCache, S3)
  - Any instance can handle any request
  - Better scalability and reliability

• **Use stickiness only if necessary**
  - Last resort for legacy applications
  - Causes uneven load distribution
  - Instance failure loses sessions

### **🎯 Important Exam Points:**
- Stickiness = **session affinity**
- Uses **cookies** (ALB) or **source IP** (NLB)
- Duration: **1 second to 7 days**
- **Stateless > Stateful** (avoid stickiness if possible)
- Store session data in: **ElastiCache, DynamoDB, or S3**
- Stickiness can cause **uneven load** distribution
- If instance fails, **sessions are lost** (unless shared storage)

---

## **Gateway Load Balancer (GWLB)**

### **Purpose:**

• **Run and scale 3rd-party network appliances**
  - Deploy network security and monitoring appliances
  - Scale them horizontally like application servers

### **Examples:**

• **Firewalls**
  - Next-generation firewalls (Palo Alto, Fortinet, etc.)

• **IDS / IPS**
  - Intrusion Detection/Prevention Systems

• **Traffic inspection systems**
  - Deep packet inspection
  - Threat detection

### **Key characteristics:**

• **Handles inbound and outbound traffic**
  - Traffic from internet to VPC
  - Traffic from VPC to internet
  - Can inspect both directions

• **Provides transparent inspection**
  - Applications don't know inspection is happening
  - No changes to application code

• **Traffic enters/exits via GWLB endpoints**
  - GWLB endpoints in VPC subnets
  - Routes traffic through appliances
  - Returns traffic to destination

• **Load balances across multiple appliances**
  - Distributes traffic to multiple instances of appliances
  - Scales horizontally for performance

• **Traffic + metadata tunneled using GENEVE**
  - GENEVE = Generic Network Virtualization Encapsulation
  - Encapsulates original packets with metadata
  - Protocol designed for network virtualization

### **Summary:**

• **GWLB = "Elastic firewall / inspection layer for VPCs"**
  - Scales security appliances like you scale applications
  - Centralized security inspection point

### **🎯 Important Exam Points:**
- GWLB = **Layer 3** (operates at IP packet level)
- Use for: **firewalls, IDS/IPS, DPI**
- **Transparent** to applications
- Uses **GENEVE protocol** (port 6081)
- Traffic flow: Source → GWLB Endpoint → GWLB → Security Appliance → GWLB → GWLB Endpoint → Destination
- GWLB is deployed in **service provider VPC**
- GWLB endpoints in **consumer VPCs**
- Use case: **centralized security** for multiple VPCs

---

## **Quick Exam Triggers - Decision Guide**

• **Need to pause ASG launch/terminate → Lifecycle Hooks**
  - Want to run custom actions during scaling events

• **App-aware health checks → ELB health checks**
  - Need to verify application is working, not just instance is running

• **Reduce CPU on EC2 → SSL offload (ALB)**
  - Move SSL termination to load balancer

• **Preserve encryption end-to-end → NLB**
  - Compliance requires unbroken encryption

• **Session-based apps → Stickiness**
  - Application requires same user to hit same instance
  - (Better: make app stateless)

• **Inline firewall/IDS at scale → GWLB**
  - Need to inspect all traffic with 3rd-party appliances

### **🎯 Most Common Exam Scenarios:**

1. **Web application with variable load** → ASG + ALB + Target Tracking
2. **Need static IPs for whitelisting** → NLB
3. **Content-based routing (paths, hosts)** → ALB
4. **Non-HTTP protocol** → NLB
5. **Scale 3rd-party security appliances** → GWLB
6. **Replace unhealthy instances automatically** → ASG with ELB health checks
7. **Run custom code on instance launch** → ASG Lifecycle Hooks
8. **Reduce instance SSL CPU usage** → ALB SSL offload
9. **Maintain sessions** → Stickiness (but prefer stateless + shared storage)

---

## **Final Exam Preparation Summary**

### **RDS Key Points:**
- Multi-AZ = **HA** (not read scaling)
- Read Replicas = **read scaling** and **fast DR**
- Encryption **cannot be removed**
- Restore = **new instance** (new endpoint)
- RDS Proxy = **connection pooling** (perfect for Lambda)

### **Aurora Key Points:**
- Aurora = **MySQL/PostgreSQL compatible**
- Storage = **shared** across all instances
- Global Database = **< 1 second** replication
- Serverless = **variable workloads**
- Backtrack = **in-place rewind**

### **Load Balancer Decision Tree:**
1. **HTTP/HTTPS** → probably ALB
2. **Need Layer 7 features** (path routing, authentication) → ALB
3. **Non-HTTP** → NLB
4. **Static IPs** → NLB
5. **Extreme performance** → NLB
6. **TLS passthrough** → NLB
7. **3rd-party appliances** → GWLB

### **Auto Scaling Key Points:**
- ASG = **free** (pay for instances only)
- Use **Launch Templates** (not Launch Configurations)
- **Target Tracking** = easiest scaling policy
- **ELB health checks** > EC2 health checks
- **Lifecycle Hooks** = custom actions on launch/terminate
- **Many small instances** > few large instances

---
