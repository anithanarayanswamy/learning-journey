# ECS Concepts (High Level)

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

---

## Quick Exam Checklist – ECS (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **ECS** | Task Definition (containers, CPU/mem, Task Role); Service (desired count, HA, LB). |

---

## Important Exam Points – ECS

- **ECS Task Role** = IAM for containers; **Service** = desired count, restarts, LB integration.