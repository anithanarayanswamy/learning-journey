## EC2 architecture quick notes

- **EC2 instances are virtual machines** running on physical **EC2 hosts**.
- **Hosts live in a single AZ**; if the underlying host or AZ has an issue, the instance can be impacted.

### What is EC2 good for?

- **Traditional OS + application compute** – you manage the OS, patches and application.
- **Long‑running compute** – web servers, app servers, databases (where managed options don’t fit).
- **Server‑style apps** – APIs, backend services, anything that needs full OS control.
- **Burst or steady‑state load** – choose instance families/sizes and Auto Scaling accordingly.
- **Monolithic app stacks** – “lift‑and‑shift” from on‑prem, legacy 3‑tier apps.
- **Migration / DR** – re‑host existing VMs to EC2, or run disaster‑recovery environments.

**Exam point (EC2 use cases)**  
- For **full OS control / custom agents / legacy software** → **EC2** (not Lambda, not Fargate).  
- For **lift‑and‑shift** or **on‑prem like servers** → **EC2**.  
- For **event‑driven / short‑lived code** → usually **not EC2**, use **Lambda**.

### Decoding EC2 instance types (high level)

- **Instance Family** – general purpose (`t`, `m`), compute (`c`), memory (`r`, `x`), storage (`i`, `d`), accelerated (`p`, `g`, `inf`).
- **Generation** – number after family, e.g. `m7g.large` → newer than `m5.large`.
- **Size** – `nano`, `micro`, `small` ... `24xlarge` – affects vCPUs, RAM, network, EBS bandwidth.

**Exam point (instance selection)**  
- **General purpose** (`t`, `m`) for *balanced* workloads (web/app servers).  
- **Compute optimized** (`c`) for CPU‑heavy (batch processing, media transcoding).  
- **Memory optimized** (`r`, `x`) for in‑memory DBs, caches, analytics.  
- **Storage optimized / instance store** (`i`, `d`) for **very high IOPS / throughput with local disks**.

---

## Elastic Block Store (EBS)

- **Block storage** – raw disk allocations (volumes) that instances format with a file system (e.g. `ext4`, `xfs`).
- **Provisioned in 🚨 ONE AZ ONLY 🚨** – resilient within that AZ but not cross‑AZ.
- **Network‑attached** to **one EC2 instance at a time** (or certain services).
- Volumes can be **detached and attached** to other instances in the **same AZ** – **persistent**, not tied to instance lifecycle.
- **Snapshots** are backups into S3. You can create new volumes from snapshots (and migrate between AZs/Regions by copying the snapshot).
- Different **volume types** give different performance and cost.
- Billed based on **GB‑month** and, for some types, **provisioned performance** (IOPS / throughput).

**Exam point (EBS basics)**  
- **EBS survives instance stop/start and terminate** (if `DeleteOnTermination=false`).  
- **One AZ only** – to make data highly available, use **EBS + multi‑AZ services** (e.g. RDS) or **replication**.  
- For **multi‑AZ shared storage**, think **EFS**, **FSx**, or **S3**, **not** EBS.

### Instance Store Volumes (ephemeral)

- **Local disks on the EC2 host** (physically attached).
- **Added at launch ONLY** – you cannot attach later to an existing instance.
- **Data is LOST** on instance move, resize or hardware failure; survives a **reboot**, but not **stop/start** when the instance is moved to a new host.
- Very **high performance** (low latency, very high IOPS/throughput).
- Cost is **included in the instance price** – you “pay for it anyway”.
- **TEMPORARY storage** – use for caches, scratch, buffers, replicated data.

More details:

- Exposed as **block devices**; instance types have zero or more instance store volumes.
- Device names often appear as `ephemeral0`, `ephemeral1`, ... depending on how many volumes the instance type supports.

**Exam point (Instance Store vs EBS)**  
- If the question says **“data must persist when the instance is stopped/terminated”** → **EBS**, **not** instance store.  
- If the question says **“highest storage performance, data can be regenerated, cache/scratch”** → **Instance Store**.  
- **Can’t detach** instance store and move it to another instance – you must **rebuild data**.

---

## EBS volume types – HDD focused

- **HDD‑based** EBS volumes are for **throughput‑oriented** workloads.
- Lower cost than SSD storage.

- **st1 – Throughput‑Optimized HDD**
  - Max around **500 IOPS**, up to **500 MB/s** throughput (depends on size).
  - For **frequent access**, **throughput‑intensive**, **sequential** workloads – big data, data warehouses, log processing.

- **sc1 – Cold HDD**
  - Max around **250 IOPS**, up to **250 MB/s**.
  - **Lowest‑cost** EBS volume type.
  - For **infrequently accessed** data, “colder” workloads that need **few scans per day**.

**Exam point (HDD vs SSD)**  
- **st1/sc1 are NOT allowed as boot volumes** – boot volume must be SSD (`gp2/gp3`, `io1/io2`).  
- For **throughput / large sequential scans** → **st1**.  
- For **cheapest storage, infrequent access** → **sc1**.

### Instance Store vs EBS – quick comparison

- **Persistence** → choose **EBS** (avoid instance store).
- **Resilience & durability** → **EBS** (with snapshots for backup).
- **Storage lifecycle independent of instance** → **EBS**.
- **Very high performance and you can lose data** → **Instance Store**.
- **Cost** – instance store is **often cheaper per GB** because it’s built into instance price.

**Exam point (performance numbers – rough)**  
- **gp2/gp3** – up to **16,000 IOPS** (general purpose SSD).  
- **io1/io2** – up to **64,000 IOPS** (and up to **256,000** on some Nitro/blocks edge cases).  
- **RAID0 across multiple EBS volumes** can go up to ~**260,000 IOPS** (combining io1/io2 or gp2/gp3).  
- **More than that / ultra‑low latency** → design with **Instance Store**.

---

## EBS Snapshots

- **Snapshots are incremental, point‑in‑time backups** of EBS volumes stored in S3.
- The **first snapshot** is a full copy of the data on the volume.
- **Subsequent snapshots are incremental** – only changed blocks are stored.
- You can **create volumes (restore)** from a snapshot in the **same or a different AZ**.
- Snapshots can be **copied to another Region** – useful for DR or migrations.

High‑level workflow example:

1. Create an EBS volume and attach it to an EC2 instance.
2. Create and mount a file system, generate a test file.
3. Detach and attach the volume to another instance in the **same AZ** – data remains intact.
4. Create an **EBS snapshot** from that volume.
5. Create a **new volume** in **another AZ** (or Region after copying snapshot) from the snapshot – file system and file are intact.

**Exam point (snapshots)**  
- **Snapshots are stored in S3 but managed by EBS**, you don’t see them as S3 objects.  
- **You can create an encrypted volume from an unencrypted snapshot and vice versa** (with KMS, and appropriate permissions).  
- **Copying a snapshot to another Region** is the standard **multi‑Region DR** pattern.

---

## EBS Encryption (KMS)

- Accounts can be configured to **encrypt new EBS volumes by default** using the **default EBS KMS key**.
- Otherwise you **explicitly choose a KMS key** (AWS‑managed or customer‑managed CMK).
- Each volume uses **one unique data encryption key (DEK)**.
- **Snapshots and future volumes from that snapshot reuse the same DEK** (wrapped with the CMK).
- You **cannot change a volume from encrypted to unencrypted in place** – must create a new volume.
- Encryption is handled by the **Nitro hardware**, the OS is **not aware** → **no meaningful performance loss** in exam questions.

**Exam point (encryption behavior)**  
- **Encrypting a volume** automatically **encrypts all its snapshots**; restoring from an **encrypted snapshot** yields an **encrypted volume**.  
- To **change KMS key or encryption setting**, you usually **snapshot → copy snapshot with new key → create new volume**.  
- For **compliance** scenarios (PII, regulated workloads), pick **EBS encryption + CMK**.

---

## EC2 Network & DNS Architecture

### Elastic Network Interface (ENI)

- An instance can have **multiple ENIs**, but **all must be in the same AZ** as the instance (can be in different subnets in that AZ).
- One **primary ENI** is attached at launch; it has:
  - A **MAC address**.
  - One **primary private IPv4** address (mandatory).
  - Zero or more **secondary private IPv4** addresses.
  - Zero or one **public IPv4 address** (can be an auto‑assigned public IP or an Elastic IP).
  - Zero or more **IPv6 addresses**.
  - **Security groups**.
  - **Source/destination check** setting.

You can attach **secondary ENIs**:

- For **separating management and data traffic** (multi‑homed).
- To use **different security groups** per interface.
- For **licensing setups** that bind software licenses to a specific **MAC address** – move the ENI between instances for failover.

**Exam point (ENI use cases)**  
- When a question mentions **“low‑cost HA / failover between instances”** using **same MAC / IP**, attach the **same ENI** to a standby instance in the same AZ.  
- **ENI is AZ‑bound** – you **cannot move** it across AZs.  
- To **change public IP** of an instance, you can **associate/disassociate an Elastic IP** to the ENI.

### Addressing and DNS

- Example:
  - **MAC Address** – e.g. `00:0d:83:b1:c0:8e`.
  - **Primary IPv4 Private IP** – e.g. `10.16.0.10` → internal DNS name like `ip-10-16-0-10.ec2.internal`.
  - **Public IPv4 Address** – e.g. `3.89.7.136` → public DNS name like `ec2-3-89-7-136.compute-1.amazonaws.com`.

- **VPC = private IP** – traffic inside the VPC uses private IP and internal DNS.
- **Outside the VPC = public IP** – internet clients use the public IP/DNS (or Elastic IP).
- **1 Elastic IP per private IPv4** address on the ENI.

Security notes:

- **Security Groups** are **stateful** and attached to ENIs.
- **Source/destination check** is **enabled by default** – the instance must be either the source or destination of the traffic.
- You **disable source/dest check** for instances acting as **NAT instances** or **routers**.

**Exam point (public IP & DNS)**  
- The **OS doesn’t see the public IPv4** – it only sees the private IP; the mapping is handled by the AWS network.  
- **Public IPv4 (non‑Elastic IP) is dynamic** – stopping and starting the instance **changes** it.  
- **Elastic IP** is **static** and can be **remapped** to another instance/ENI in the same Region.  
- The **public DNS name resolves to the private IP inside the VPC**, and to the public IP from the internet.

---