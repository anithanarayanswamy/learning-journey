# Amazon Machine Image (AMI)

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

## Quick Exam Checklist – AMI (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **AMI** | Regional; copy to other regions (snapshots copied); private by default; immutable (create new to update). |

---

## Important Exam Points – AMI

- **AMI**: **Regional**; **immutable** (launch → update → create new AMI); copy to other region copies snapshots.