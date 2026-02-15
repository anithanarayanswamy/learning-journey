# EC2 Savings Plans

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

## Quick Exam Checklist – Savings Plans (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Savings Plans** | $/hour commitment; Compute (EC2, Fargate, Lambda, flexible); EC2 Instance (instance family, region). |

---

## Important Exam Points – Savings Plans

- **Savings Plans**: Commit **$/hour**; **Compute SP** = EC2, Fargate, Lambda, any region/family; **EC2 Instance SP** = higher discount, specific family/region.