## Amazon Machine Image (AMI)

An **Amazon Machine Image (AMI)** is a template used to launch EC2 instances. It bundles:
- **Root volume** (OS + preinstalled software)
- **Launch permissions** (who can use it)
- **Block device mappings** (additional volumes to attach)

- **Sources of AMIs**
  - **AWS-provided**: Standard, patched OS images maintained by AWS.
  - **Marketplace AMIs**: Published by vendors, can include **licensed/commercial software** (e.g., firewall appliances, databases).
  - **Community AMIs**: Shared by other AWS customers; use with caution (security, support).

- **Regional scope**
  - AMIs are **regional** – identified by a **regional unique ID** (e.g., `ami-0a887e401f7654935`).
  - **They only work within the region where they are created**, but **can be copied** to other regions (this also copies underlying snapshots).

- **Permissions**
  - **Private (default)**: Only your account can use the AMI.
  - **Public**: Anyone in AWS can launch from the AMI.
  - **Specific accounts**: Share AMI with selected AWS account IDs.

- **Creating AMIs (AMI baking)**
  - Launch a **base EC2 instance**, install and configure OS, patches, and application.
  - **Create an AMI** from the configured instance – this process is called **AMI baking**.
  - **AMIs are immutable**: You **cannot edit** an existing AMI. To change it:
    - Launch an EC2 instance from the AMI.
    - Modify configuration/software.
    - **Create a new AMI**.

- **Important exam points (AMI)**
  - **AMI is regional** – works only in its **own region**, but **can be copied** to others.
  - **Default permissions**: AMI is **private to your account**.
  - To **update an AMI**, you **must create a new one** (launch → update → create new AMI).
  - When an AMI is copied to another region, its **snapshots are copied** as well.

---

## EC2 Purchase Options – On-Demand

**On-Demand instances** are the default and simplest purchasing model.

- **Characteristics**
  - **No long-term commitment** and **no upfront payment**.
  - **Per-second billing** while the instance is in **running** state.
  - **Isolated instances** but run on **shared underlying hardware** with other customers.
  - **No capacity reservation** by default (capacity is usually available, but not guaranteed during major spikes).

- **When to use**
  - **Short-term workloads**.
  - **Unpredictable or unknown workloads** (e.g., new applications).
  - **Applications that cannot tolerate interruptions**.

- **Pros**
  - **Predictable pricing** per hour/second.
  - **Maximum flexibility** – start/stop/terminate anytime.
  - **No upfront cost**, **no long-term contract**.

- **Cons**
  - **Most expensive** option over the long term (no inherent discount).
  - **No capacity reservation** unless combined with On-Demand Capacity Reservations.

- **Important exam points (On-Demand)**
  - Best for **unpredictable** usage and **critical workloads** that **cannot be interrupted**.
  - **Default** purchase option if no other is selected.
  - Billed **per second** while running (for Linux; Windows often per hour – check wording).

---

## EC2 Purchase Options – Spot

**Spot Instances** let you use **spare EC2 capacity** at a **large discount**.

- **Pricing**
  - AWS sells **unused EC2 capacity** at up to **90% discount** compared to On-Demand.
  - Price is based on **current spare capacity** in that instance pool.

- **Interruption model**
  - Instances **can be interrupted** by AWS when they need the capacity back.
  - You receive a **2-minute interruption notice**.
  - Interruptions can **stop, hibernate, or terminate** instances depending on your configuration.

- **Use cases**
  - **Non-time-critical** workloads.
  - Jobs that **can be retried or restarted** (idempotent).
  - **Bursty capacity** needs where you scale out when cheap capacity is available.
  - **Stateless** applications or workers (no important state stored locally).

- **Never use Spot for**
  - **Critical workloads** that **cannot tolerate interruptions**.
  - **Stateful** systems without proper checkpointing or external state storage.

- **Important exam points (Spot)**
  - Up to **90% cheaper** than On-Demand.
  - AWS can **reclaim capacity at any time** with a **2-minute warning**.
  - Ideal for **batch processing**, **big data**, **CI/CD**, **image/video processing**, and other **stateless, fault-tolerant** workloads.

---

## EC2 Purchase Options – Reserved Instances (RI)

**Reserved Instances** provide **billing discounts** in exchange for a **1-year or 3-year commitment**.

- **Concept**
  - You commit to a **specific instance family**, **scope** (Region or AZ), **platform**, and sometimes **tenancy**.
  - You pay for the **entire term**, either all upfront, partial upfront, or no upfront.

- **Payment options**
  - **All Upfront (AURI)**: Pay full cost at the start, **maximum discount**, **no per-second charge** during term.
  - **Partial Upfront (PURI)**: Some upfront, smaller per-second rate, **medium discount**.
  - **No Upfront (NURI)**: No upfront, higher per-second rate, **lowest discount**, but still cheaper than On-Demand.

- **Scope**
  - **Regional RIs**:
    - Apply to **any AZ** in the region for **matching instance attributes**.
    - Provide **billing discount only** (no capacity reservation).
  - **Zonal RIs**:
    - Applied to a **specific AZ**.
    - Provide **billing discount** **and** **capacity reservation** in that AZ.

- **Behavior**
  - If you **don’t launch instances** that match the RI, you are **still billed** for the reservation.
  - **Larger instances** may be **partially covered** by multiple smaller RIs (depending on RI type and normalization factors).

- **When to use**
  - **Steady-state**, predictable workloads.
  - Applications running **24/7** for **long term**.

- **Important exam points (RIs)**
  - Commit for **1 or 3 years** for **discounted pricing** compared to On-Demand.
  - **Unused RIs still incur charges** – discount applies only if you run matching instances.
  - **Zonal RI** = **discount + capacity reservation**. **Regional RI** = **discount only**.

---

## EC2 Purchase Options – Dedicated Hosts

**Dedicated Hosts** provide **physical servers** fully dedicated to your account.

- **Characteristics**
  - You get **visibility and control** over **sockets, cores, and host-level hardware**.
  - Supports **BYOL (Bring Your Own License)** for software licensed per **socket/core/host** (e.g., Windows Server, Oracle).
  - **Host affinity** can pin instances to specific hosts for compliance/audit reasons.

- **Billing**
  - You **pay per host**, not per instance.
  - **No instance charges** – all instances on that host are covered by the host’s cost.

- **Use cases**
  - **Compliance** or **regulatory** requirements (e.g., dedicated hardware).
  - **Licensing requirements** based on sockets/cores/physical hosts.

- **Important exam points (Dedicated Hosts)**
  - Use when you need **per-socket/core licensing** or **strict compliance isolation**.
  - **Dedicated Host = physical server**, **Dedicated Instance = dedicated hardware but less control** (know the distinction).

---

## Scheduled Reserved Instances

**Scheduled RIs** are for **recurring, predictable usage** that doesn’t run 24/7.

- **Characteristics**
  - Reserve capacity for **specific time windows** (e.g., every day 23:00–04:00).
  - Good for **batch jobs**, **nightly processing**, or **weekly analysis**.
  - Minimum of about **1,200 hours per year** and **1-year term** (exam-level detail).
  - **Not available for all regions and instance types**.

- **Use cases**
  - **Daily batch processing** for a few hours.
  - **Weekly analytics** or data processing jobs.
  - When you need **capacity guarantee** for **recurring but not continuous** workloads.

- **Important exam points (Scheduled RIs)**
  - Designed for **recurring, scheduled usage**, not continuous.
  - Provide **capacity reservation** during the **scheduled window**.

---

## Capacity Reservations & On-Demand Capacity Reservations

**Capacity Reservations** allow you to **reserve capacity** in a specific AZ without a long-term commitment (if using On-Demand Capacity Reservations).

- **Standard RIs vs Capacity Reservations**
  - **Regional RIs**:
    - Give **discounts** across the region.
    - **Do not reserve capacity** in any AZ.
  - **Zonal RIs**:
    - Apply to one AZ.
    - Provide **both discount and capacity reservation** for that AZ.

- **On-Demand Capacity Reservations**
  - Let you **reserve capacity in a specific AZ** at **full On-Demand price**.
  - **No term commitment** – you can create and cancel anytime.
  - You **pay whether or not** you are actually using the instances (while reservation is active).
  - Can be combined with **RIs or Savings Plans** for billing discount if they match.

- **Use cases**
  - **High-priority** or **disaster recovery** workloads where capacity must be guaranteed.
  - Handling **major faults** or **regional events** where capacity might be scarce.

- **Important exam points (Capacity Reservations)**
  - **On-Demand Capacity Reservation** = **capacity guarantee** in AZ, **no discount**, **On-Demand pricing**.
  - **Regional RI** = **discount only**, **no capacity guarantee**.
  - **Zonal RI** = **discount + capacity reservation**.

---

## EC2 Savings Plans

**Savings Plans** are a **flexible discount model** where you commit to a **$ per hour spend** for 1 or 3 years.

- **Concept**
  - You commit to **spend a specific amount per hour** (e.g., **$20/hour**) on eligible compute usage.
  - Your usage **up to that commitment** is billed at a **reduced Savings Plan rate**.
  - Usage **beyond the commitment** is billed at **On-Demand rate**.

- **Types**
  - **Compute Savings Plans**
    - Most **flexible**: apply to **any region**, **any instance family**, **any OS**, **any tenancy**, and to **EC2, Fargate, and Lambda** usage.
  - **EC2 Instance Savings Plans**
    - Less flexible: tied to **specific instance family** in a **specific region**.
    - Higher **discount** than Compute Savings Plans.

- **Comparison with RIs**
  - Savings Plans are **more flexible** – easier when workloads change.
  - RIs can provide **similar or slightly higher discounts** but are **less flexible**.

- **Important exam points (Savings Plans)**
  - You commit to **$ per hour spend**, not specific instances.
  - Apply to **EC2, Fargate, and Lambda** (for **Compute Savings Plans**).
  - Usage **above** your commitment is charged at **On-Demand**.

---

## Vertical Scaling (Scale Up / Down)

**Vertical scaling** means **changing instance size** (e.g., from `t3.small` to `t3.large`).

- **Characteristics**
  - Requires a **stop → change instance type → start** cycle (i.e., **reboot**, causing **disruption**).
  - Larger instances typically have a **pricing premium**.
  - There is an **upper bound** on performance – you can only go as large as the **largest instance type**.

- **Pros**
  - **Simple to implement** – **no application change** required.
  - Works for **almost any application**, including **monolithic** apps.

- **Cons**
  - **Downtime** during scaling.
  - **Limited maximum capacity**.
  - Can be **more expensive** than scaling out multiple smaller instances.

- **Important exam points (Vertical Scaling)**
  - Requires **reboot / downtime**.
  - Used when **application can’t be easily distributed** across multiple instances.

---

## Horizontal Scaling (Scale Out / In)

**Horizontal scaling** means **adding or removing instances** behind a load balancer.

- **Characteristics**
  - Typically uses an **Auto Scaling Group (ASG)** to add/remove instances.
  - Requires **stateless** application or **off-host session storage** (e.g., ElastiCache, DynamoDB, RDS).
  - **No disruption** to the application when scaling if properly designed.
  - **No theoretical upper limit** – can keep adding more instances.

- **Pros**
  - Often **more cost-effective**: use many **smaller instances** instead of one big one.
  - **Granular scaling** – scale by small increments.
  - Provides **high availability** and **fault tolerance** across AZs.

- **Cons**
  - Requires **application design changes** (stateless, external session storage, etc.).

- **Important exam points (Horizontal Scaling)**
  - Requires **session management** – use **sticky sessions** or **external session store**.
  - Natural fit with **Elastic Load Balancing** and **Auto Scaling Groups**.

---

## EC2 Instance Metadata

**Instance Metadata Service (IMDS)** provides **information about the instance** to applications **running on that instance**.

- **Access**
  - Available at the **special link-local address** `http://169.254.169.254`.
  - Only accessible **from inside the instance**.

- **Information available**
  - **Environment**: instance ID, AMI ID, instance type, region/AZ.
  - **Networking**: IP addresses, security groups, etc.
  - **Authentication**: IAM **role credentials** for the instance profile.
  - **User data**: bootstrap script provided at instance launch.

- **Security**
  - Traditionally **not authenticated or encrypted** (plain HTTP).
  - AWS introduced **IMDSv2** (recommended) which uses **session tokens** to mitigate SSRF-style attacks.

- **Important exam points (Instance Metadata)**
  - Accessed via **`http://169.254.169.254`** **from within the instance only**.
  - Used by applications and the AWS SDK to **retrieve IAM role credentials**.
  - **Never expose** the metadata endpoint to the internet.

---

## ECS Concepts (High Level)

Amazon ECS is a **container orchestration service** for running Docker containers on AWS.

- **Container Definition**
  - Part of a Task Definition.
  - Defines **container image**, **ports**, **environment variables**, **logging**, and other container-specific settings.

- **Task Definition**
  - A **blueprint** for your application.
  - Specifies **one or more container definitions**, **CPU/Memory**, **network mode**, **IAM Task Role**, and volumes.
  - Versioned – you can have multiple revisions.

- **Task Role**
  - An **IAM role** that the **task assumes**.
  - Used so **containers can call AWS services** securely **without embedding long-term credentials**.

- **Service**
  - Defines **how many copies (tasks)** of your Task Definition to run.
  - Manages **high availability** and **restarts** failed tasks.
  - Can integrate with **load balancers** for traffic distribution.

- **Important exam points (ECS)**
  - **Task Role** = **IAM role for the task containers** (fine-grained permissions).
  - **Task Definition** describes **containers, resources, and roles**.
  - **Service** ensures desired **number of tasks** are running and handles **restarts/HA**.

