# Day 16 – CFN-INIT, CFN-SIGNAL, Instance Roles, ECR, Bootstrapping, Placement Groups & More – SAA-C03

## CFN-INIT and CFN-SIGNAL

### CFN-INIT (AWS::CloudFormation::Init)

**CFN-INIT** is a desired-state configuration engine that is part of the CloudFormation suite. Unlike **User Data**, which is procedural (run these commands in order), **cfn-init** lets you declare a **desired state** for the instance (e.g. “these packages must be installed, these files must exist, this service must be running”). The cfn-init helper script then performs whatever actions are needed to bring the instance into that state.

- **Where it runs**: The **cfn-init** helper script is installed on the EC2 AMI (or installed via User Data first). It reads configuration from the resource’s **Metadata** block under **AWS::CloudFormation::Init** in the CloudFormation template.
- **What you can configure**:
  - **Packages** – install via yum, apt, etc.
  - **Groups / Users** – create OS groups and users.
  - **Sources** – download files from URLs (e.g. S3, HTTP).
  - **Files** – create or update files on disk (e.g. config files).
  - **Commands** – run arbitrary commands (e.g. after packages/files are in place).
  - **Services** – ensure OS services are enabled and running (e.g. systemd).
- **Procedural (User Data) vs Desired State (cfn-init)**:
  - **User Data**: “Run this script step by step.” If something fails mid-way, the rest may not run; no built-in retry or idempotency.
  - **cfn-init**: “The instance should look like this.” cfn-init can be run multiple times and will converge toward that state; better for retries and consistency.

**Important exam points (CFN-INIT)**

- **cfn-init** is used for **desired-state configuration** on EC2 from CloudFormation; User Data is **procedural**.
- Configuration is provided via **Metadata** and **AWS::CloudFormation::Init** on the CFN resource (e.g. AWS::EC2::Instance).
- **Packages, Groups, Users, Sources, Files, Commands, Services** are the main sections under `AWS::CloudFormation::Init`.
- If the question asks for “declarative” or “desired state” config on EC2 in CloudFormation → think **cfn-init**.

---

### Creation Policies and CFN-SIGNAL

**Creation policies** add a **wait condition** to a CloudFormation resource. The resource does **not** transition to **CREATE_COMPLETE** (and the stack does not proceed) until the instance **signals** success using the **cfn-signal** tool.

- **Flow**:
  1. CloudFormation creates the resource (e.g. EC2 instance) and runs User Data / cfn-init.
  2. Creation policy says: “Do not mark this resource as CREATE_COMPLETE until a signal is received.”
  3. From inside the instance, after bootstrapping (and optionally cfn-init) completes successfully, a script runs **cfn-signal** with the stack name, logical resource ID, and (optionally) a wait condition URL.
  4. When CloudFormation receives the signal, it marks the resource **CREATE_COMPLETE**. If the signal is not received within the timeout, the resource (and often the stack) fails.
- **Why use it**: Ensures the stack does not report “success” until the instance has actually finished installing software, pulling config, and starting services. Without signalling, CREATE_COMPLETE only means “the instance was launched,” not “the instance is ready.”

**Important exam points (Creation Policy / cfn-signal)**

- **Creation policies create a WAIT STATE** – the resource stays in a “waiting” state until **cfn-signal** is called from the instance.
- **cfn-signal** is run **from inside the instance** (e.g. at the end of User Data or after cfn-init) to tell CloudFormation that the resource is ready.
- If the signal is **not** received within the timeout, the resource creation fails (and the stack can fail or roll back, depending on design).
- Use creation policies when the exam scenario requires “don’t consider the stack/instance ready until bootstrapping has completed successfully.”

---

## EC2 Instance Roles and Instance Profiles

**Instance roles** are IAM roles that you attach to an EC2 instance. **Instance profiles** are the container that allows an EC2 instance to assume a role; when you attach a role to an instance, AWS uses an instance profile (you create/select the role, and the profile is created or linked automatically in the launch wizard/CLI).

- **How it works**:
  - You create an IAM role with permissions (e.g. S3 read, SSM, CloudWatch Logs).
  - You attach that role to the instance at launch (or later). The instance gets an **instance profile** that references that role.
  - Applications on the instance do **not** use long-term access keys. They use **short-lived temporary credentials** that are available via the **Instance Metadata Service** and are **automatically rotated** by EC2 and STS.
- **Where credentials live**:  
  `http://169.254.169.254/latest/meta-data/iam/security-credentials/<role-name>`  
  The response includes `AccessKeyId`, `SecretAccessKey`, `Token`, and expiration. The AWS CLI and SDKs on the instance **automatically** use these credentials if no other credentials are configured.
- **Best practice**: Always use instance roles for EC2 access to AWS services; **do not** store IAM user access keys on instances.

**Important exam points (Instance roles / profiles)**

- **Instance roles + instance profiles** are how EC2 applications get permissions to call AWS APIs; credentials are **temporary** and **automatically rotated**.
- Credentials are provided via **EC2 Instance Metadata** (`169.254.169.254`), not stored on disk.
- **Always prefer instance roles over embedding access keys** in AMIs or User Data – exam questions often reinforce this.
- Path for credentials in metadata: **iam/security-credentials/role-name**.

---

## ECS Launch Types – EC2 vs Fargate (Quick Reference)

- **Containers** → use **ECS** (or EKS).
- **Large workload, cost-sensitive** → **ECS on EC2** (you manage instances; good for steady utilization and RIs/Savings Plans).
- **Large workload, want minimal ops** → **Fargate** (no instance management).
- **Small / burst workloads** → **Fargate** (pay per task, per second).
- **Batch / periodic workloads** → **Fargate** (run when needed, stop when done).

**Important exam points (ECS vs Fargate)**

- “No servers to manage” / “no EC2 to manage” → **Fargate**.
- “Lowest cost for large, steady container workload” → **ECS on EC2** with good utilization and reserved capacity.
- “Spiky, batch, or periodic” → **Fargate**.

---

## Elastic Container Registry (ECR) – Summary

- **Managed container image registry** (like Docker Hub, but for AWS).
- **Structure**: One account has **public** and **private** registries → **repositories** → **images** → **tags**.
- **Public**: Read-only by default for anonymous users; **write (push)** requires permissions.
- **Private**: All read and write require IAM (or resource) permissions.
- **Integrations**:
  - **IAM** for access control.
  - **Image scanning**: basic on push; **enhanced** with Amazon Inspector.
  - **CloudWatch** for metrics (auth, push, pull, etc.).
  - **CloudTrail** for API actions.
  - **EventBridge** for events (e.g. image push, scan results).
  - **Replication**: **cross-Region** and **cross-account**.

**Important exam points (ECR)**

- Need a **private, AWS-native** registry with **IAM** → **ECR**.
- Need **vulnerability scanning** for images → ECR **image scanning** (basic or enhanced/Inspector).
- Need to **replicate images** to other regions or accounts → ECR **replication**.
- Public ECR = public **read**; **write** always needs permissions.

---

## EC2 Bootstrapping and User Data

- **Bootstrapping** = automating EC2 setup at launch (install software, pull config, start services).
- **User Data** is available inside the instance at:  
  `http://169.254.169.254/latest/user-data`
- The **OS** runs the User Data (e.g. bash on Linux); **EC2 does not execute or interpret it**.
- User Data runs **only at first launch** (by default), not on every reboot.
- For **desired-state** config from CloudFormation, use **cfn-init** (see above).

**Important exam points (Bootstrapping)**

- User Data is **executed by the OS**, not by the EC2 service.
- **Only on launch** (first boot), unless the script is written to run on every boot.
- “Automatically configure EC2 at launch” → **User Data** and/or **cfn-init**.

---

## AWS::CloudFormation::Init (cfn-init) – Recap

- **cfn-init** is a helper script on the EC2 AMI that reads **Metadata → AWS::CloudFormation::Init** from the CloudFormation resource.
- It provides a **simple desired-state** config: **Packages, Groups, Users, Sources, Files, Commands, Services**.
- **Procedural** = User Data; **Desired state** = cfn-init.
- Often used together: User Data calls cfn-init, then cfn-signal when done (when using creation policies).

---

## SSM Parameter Store

Parameter Store is part of **AWS Systems Manager**. It stores configuration and secrets as **parameters** (e.g. DB passwords, API keys, config strings).

- **Hierarchy**: Parameters can be **flat** (e.g. `myDBpassword`) or **hierarchical** (e.g. `/wordpress/DBUser`, `/wordpress/DBPassword`, `/my-cat-app/`, `/dev-team-passwords/`). Hierarchical paths help organize and scope IAM policies.
- **Use cases**: Centralize config for EC2, Lambda, ECS; avoid hardcoding secrets; share config across apps (e.g. `/wordpress/` for a WordPress stack).
- **Events**: Parameter **changes** can emit events (e.g. to **EventBridge**), so you can trigger automation when someone updates a parameter.

**Important exam points (Parameter Store)**

- Store **secrets and config** (e.g. DB credentials, app config) in **SSM Parameter Store**; use **hierarchical** paths for organization and IAM.
- **Changes to parameters** can generate **events** (e.g. EventBridge) for automation.
- Prefer **Parameter Store** (or Secrets Manager for rotation) over storing secrets in User Data or config files.

---

## Logging on EC2

- **CloudWatch** = metrics (CPU, disk, custom metrics).
- **CloudWatch Logs** = log storage and analysis (log groups, streams).
- **Neither** CloudWatch nor CloudWatch Logs **automatically** capture what’s inside an EC2 instance (files, stdout/stderr of apps). You need an **agent** that runs on the instance.
- **CloudWatch Agent** runs on the instance, reads log files and/or metrics, and sends them to CloudWatch Logs and CloudWatch. It requires:
  - **Installation** (e.g. via User Data or SSM).
  - **Configuration** (which files to tail, which metrics to collect).
  - **IAM permissions** for the instance role (e.g. `logs:CreateLogStream`, `logs:PutLogEvents`, `cloudwatch:PutMetricData`).

**Important exam points (EC2 logging)**

- To get **logs from inside an EC2 instance** into CloudWatch Logs → you **must** use **CloudWatch Agent** (or another agent that pushes to CloudWatch).
- **CloudWatch** = metrics; **CloudWatch Logs** = logs; neither “see inside” the instance without an agent.
- Instance must have an **IAM role** with permissions for CloudWatch Logs (and CloudWatch if sending metrics).

---

## Cluster Placement Groups

Placement groups influence **where** EC2 instances are placed relative to each other (same rack, spread across racks, or in partitions).

- **Cluster placement group**:
  - Instances are placed **close together** (low latency, high throughput).
  - **One AZ only** – cannot span multiple AZs; the AZ is **fixed when you launch the first instance**.
  - Can span **VPC peering** but with potential performance impact.
  - **Supported instance types** required (many current-gen types are supported).
  - **Best practice**: Use the **same instance type** and **launch all instances together** for optimal placement (not mandatory but strongly recommended).
  - **Use case**: High performance computing (HPC), tightly coupled workloads, low latency (e.g. 10 Gbps single-stream performance).

**Important exam points (Cluster placement)**

- **Cluster placement group = ONE AZ only**; AZ is **locked at first launch**; **cannot span AZs**.
- Can span **VPC peers** but may impact performance.
- Use for **low latency, high throughput** (e.g. HPC, big data).

---

## Spread Placement Groups

- **Spread** = each instance is on **different hardware** (different rack); each rack has its own network and power.
- **Benefit**: **Maximum availability** – failure of one rack does not take out multiple instances.
- **Limit**: **7 instances per AZ** (hard limit). Not supported for **Dedicated Instances** or **Dedicated Hosts**.
- **Use case**: Small number of **critical** instances that must be **isolated** from each other (e.g. a few master nodes).

**Important exam points (Spread placement)**

- **7 instances per AZ max** for spread placement groups.
- **Each instance on a different rack** – infrastructure isolation.
- **Not supported** for Dedicated Instances or Dedicated Hosts.
- “Keep a few critical instances as isolated as possible” → **Spread**.

---

## Partition Placement Groups

- **Partitions** = logical groups of racks. You get **up to 7 partitions per AZ**.
- Instances can be placed in a **specific partition** or **auto-placed**.
- **Use case**: **Topology-aware** applications (e.g. **HDFS, HBase, Cassandra**) where you want to spread replicas across partitions so that a **failure in one partition** only affects part of the application.
- **Benefit**: Contain the **blast radius** of a rack/partition failure.

**Important exam points (Partition placement)**

- **7 partitions per AZ**; instances go into partitions (manual or automatic).
- Good for **HDFS, HBase, Cassandra** and similar topology-aware apps.
- **Limit impact of failure** to one partition → **Partition placement group**.

---

## EC2 Dedicated Hosts

- A **Dedicated Host** is a **physical server** dedicated to your account. You place EC2 instances on it; you **pay for the host**, not per-instance (no separate instance charges for that host).
- **Instance family**: Host is for a specific family (e.g. **a1, c5, m5**). You see **physical sockets and cores** on the host.
- **Purchasing**: **On-Demand** or **Reserved** (Reserved Hosts can reduce cost).
- **Limitations**:
  - **AMIs**: RHEL, SUSE Linux, and **Windows AMIs are not supported** on Dedicated Hosts.
  - **Amazon RDS** instances **cannot** run on Dedicated Hosts (RDS is managed; Dedicated Hosts are for EC2).
  - **Placement groups** are **not supported** for instances on Dedicated Hosts.
- **Sharing**: Hosts can be **shared with other accounts in the same AWS Organization** (e.g. via RAM – Resource Access Manager).

**Important exam points (Dedicated Hosts)**

- **No instance charge** – you pay for the **host**; then you run instances on it (same family).
- **RHEL, SUSE, Windows** AMIs **not supported** on Dedicated Hosts.
- **RDS** does **not** run on Dedicated Hosts.
- **Placement groups** are **not** supported with Dedicated Hosts.
- **Sharing** with other accounts → **Organization / RAM**.

---

## Enhanced Networking

- **Enhanced Networking** uses **SR-IOV** (Single Root I/O Virtualization): the NIC is virtualization-aware, so the instance gets higher performance and lower CPU overhead.
- **Benefits**:
  - **Higher bandwidth** and **more packets per second (PPS)**.
  - **Lower latency** and more consistent performance.
  - **Lower host CPU** usage for networking.
- **Cost**: **No additional charge**; available on **most current EC2 instance types** (e.g. C5, M5, R5, etc.). For **maximum network performance**, use **ENA** (Elastic Network Adapter); older “enhanced networking” used Intel VF. Exam often refers to “enhanced networking” as a feature that improves I/O and PPS.

**Important exam points (Enhanced networking)**

- **SR-IOV** – no extra charge; **higher I/O, more PPS, lower latency**, lower CPU for network.
- Available on **most** modern instance types.
- When the scenario asks for “best network performance” or “high PPS” on EC2 → **instance with enhanced networking** (and often **cluster placement** if low latency between instances is needed).

---

## Quick Exam Checklist – Day 16

| Topic | Key point |
|-------|-----------|
| **cfn-init** | Desired state; Metadata + AWS::CloudFormation::Init; Packages, Files, Commands, Services, etc. |
| **cfn-signal / Creation policy** | Wait state until instance signals; cfn-signal from inside instance; timeout = failure. |
| **Instance roles** | Temporary creds via metadata; auto-rotated; use roles, not access keys on instances. |
| **ECS vs Fargate** | No servers / batch / burst → Fargate; large, cost-optimized → ECS on EC2. |
| **ECR** | IAM, scanning, replication (cross-Region, cross-account); public = R/O; private = permissions. |
| **User Data** | Run by OS, once at launch; 169.254.169.254/latest/user-data. |
| **Parameter Store** | Config/secrets; hierarchical paths; changes can trigger events. |
| **EC2 logging** | CloudWatch Logs from inside instance → **CloudWatch Agent** + IAM. |
| **Cluster placement** | One AZ only; low latency; same type + same time recommended. |
| **Spread placement** | 7 per AZ; one instance per rack; not for Dedicated. |
| **Partition placement** | 7 partitions per AZ; HDFS, Cassandra, etc.; limit failure impact. |
| **Dedicated Hosts** | Pay for host; no RHEL/SUSE/Windows; no RDS; no placement groups; can share via RAM. |
| **Enhanced networking** | SR-IOV; no charge; higher PPS, lower latency. |
