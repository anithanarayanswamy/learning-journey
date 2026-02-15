## ECS, Fargate, ECR & EC2 Bootstrapping – SAA-C03 Notes

### ECS Launch Types – EC2 vs Fargate

ECS (Elastic Container Service) is a fully managed container orchestration service. It supports two main launch types:

- **EC2 launch type (ECS on EC2)**
  - You run an ECS cluster on your own EC2 instances.
  - ECS schedules **tasks** (containers) onto the EC2 instances in the cluster.
  - **You are responsible for**:
    - Choosing instance types and sizes.
    - Patching and hardening the OS/AMI.
    - Scaling the EC2 Auto Scaling group.
    - Managing capacity, reservations, and utilization.
  - **Billing model**: You pay for the EC2 instances (and EBS, etc.) **24/7**, whether the containers are busy, idle, or stopped.
  - **When it’s a good fit**:
    - Large, steady workloads where you can keep instances highly utilized.
    - You want maximum control over the host (e.g., special agents, custom drivers).
    - Price-sensitive environments where committing to Savings Plans / RIs for EC2 makes sense.

- **Fargate launch type**
  - Serverless compute for containers – **no EC2 instances to manage**.
  - You define CPU/memory per task; AWS provisions and manages the underlying infrastructure.
  - Each task gets its own ENI (Elastic Network Interface) injected into your VPC subnets.
  - **Billing model**: You pay **per vCPU and GiB of memory requested, per second**, only while the task is running.
  - **When it’s a good fit**:
    - Small, spiky, or burst workloads.
    - Batch or periodic jobs (run, finish, and stop paying).
    - Teams that want minimal ops overhead – no capacity planning.

- **Quick decision summary**
  - **If you use containers → ECS** (or EKS, but exam often focuses on ECS here).
  - **Large workload – price conscious → ECS on EC2** (good utilization, cheaper when you keep instances busy).
  - **Large workload – operations/overhead conscious → Fargate** (you pay more per unit, but you don’t manage instances).
  - **Small / Burst workloads → Fargate**.
  - **Batch / Periodic workloads → Fargate**.

- **Important exam points (ECS vs Fargate)**
  - **If the question emphasizes “no servers to manage / no infrastructure management” → Fargate.**
  - **If the question emphasizes “optimize cost for large steady workloads” → ECS on EC2.**
  - **If required to install custom agents on host, customize OS, or use special networking modules → ECS on EC2.**
  - **If workloads are spiky / unpredictable and you want per‑task billing → Fargate.**

---

### Elastic Container Registry (ECR)

ECR is a **fully managed container image registry** similar to Docker Hub, but integrated with AWS.

- **Core concepts**
  - Each AWS account has:
    - A **private registry** by default.
    - Optionally, **public registries** for sharing images publicly.
  - A **registry** contains multiple **repositories**.
  - Each **repository** contains multiple **images**.
  - Each image can have multiple **tags** (e.g., `v1`, `latest`, `prod`).

- **Access control**
  - **Private ECR**:
    - All read (pull) and write (push) operations require IAM permissions.
    - You typically use IAM roles for EC2/ECS tasks or IAM users for CI/CD.
  - **Public ECR**:
    - Public read (pull) allowed without auth.
    - Write (push) and management operations require IAM permissions.

- **Security & integrations**
  - **IAM integration** for auth and authorization (who can push/pull).
  - **Image scanning**:
    - **Basic scanning** on push.
    - **Enhanced scanning** (powered by Amazon Inspector) for deeper, continuous scanning.
  - **Logging & events**:
    - API calls logged in **CloudTrail**.
    - **CloudWatch metrics** (auth, push, pull, storage, etc.).
    - **EventBridge** integration for triggering workflows on push/delete, scan findings, etc.
  - **Replication**:
    - **Cross‑Region** replication (copy images to other regions).
    - **Cross‑Account** replication (share images between accounts).

- **Typical workflow**
  - Build image locally or in CI.
  - Authenticate Docker client to ECR with AWS CLI (`aws ecr get-login-password`).
  - Tag the image with the ECR repository URI.
  - Push the image to ECR.
  - ECS/EKS tasks pull the image from ECR using task role permissions.

- **Important exam points (ECR)**
  - **Need a private, AWS‑integrated Docker image registry with IAM‑based control → ECR.**
  - **Need to replicate container images to multiple regions/accounts automatically → ECR replication.**
  - **Need vulnerability scanning for container images → ECR image scanning (basic or enhanced/Inspector).**
  - **Publicly share container images with the community but keep push restricted → ECR Public.**

---

### EC2 Bootstrapping & User Data

Bootstrapping is the process of **automatically configuring an EC2 instance at launch**. This is most commonly done using **User Data**.

- **User Data basics**
  - User Data is provided when launching an instance (console, CLI, CloudFormation, etc.).
  - Inside the instance, it is accessible via the metadata endpoint:
    - `http://169.254.169.254/latest/user-data`
  - The **OS, not EC2, interprets the User Data**:
    - For Linux, usually shell scripts (`#!/bin/bash`).
    - For Windows, PowerShell or batch scripts.
  - By default, User Data is run **only once at first boot** (unless you modify the OS behavior).

- **Typical bootstrapping actions**
  - Install packages and updates (`yum`, `apt`, `choco`).
  - Download application code or artifacts from S3 / CodeDeploy / Git.
  - Configure services (web servers, agents, logging, etc.).
  - Register the instance with a configuration management system.

- **Why bootstrapping matters**
  - Enables **immutable infrastructure** and repeatable builds.
  - Avoids having to bake everything into a single AMI (or can complement AMI baking).
  - Works well with Auto Scaling – new instances self‑configure on launch.

- **Important exam points (EC2 bootstrapping)**
  - **User Data scripts run at initial boot only (by default), not at every restart.**
  - **User Data is executed by the OS, not interpreted by the EC2 service itself.**
  - **Use User Data to install software, pull configs from S3, join clusters, register with services, etc., during first startup.**
  - **If an exam question mentions “automatically install and configure software on EC2 at launch” → think User Data (bootstrapping).**

---

## Quick Exam Checklist – Day 15 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **ECS on EC2** | You manage instances; good for large, steady, cost-optimized; pay for EC2 24/7. |
| **Fargate** | No servers; pay per vCPU/memory per second; batch, spiky, minimal ops. |
| **ECS vs Fargate** | No servers / batch / burst → Fargate; large cost-optimized → ECS on EC2. |
| **ECR** | Private/public registry; IAM; image scanning; replication (cross-Region, cross-account). |
| **ECR Public** | Read without auth; write needs IAM. |
| **User Data** | Run by OS at first boot only; 169.254.169.254/latest/user-data; bootstrapping. |

---

## Important Exam Points – Day 15

- **"No servers to manage"** or **"no EC2 to manage"** → **Fargate**.
- **"Lowest cost for large steady container workload"** → **ECS on EC2** (good utilization, RIs/Savings Plans).
- **Spiky, batch, periodic** → **Fargate** (pay per task, per second).
- **ECR**: **Private** = IAM for push/pull; **Public** = read anonymous, write IAM; **replication** for multi-region/account; **scanning** for vulnerabilities.
- **User Data**: **Executed by OS**, not EC2 service; **first boot only** (by default); use for install, pull config, register.
- **Bootstrapping** = User Data and/or cfn-init; "automatically configure at launch" → User Data.

