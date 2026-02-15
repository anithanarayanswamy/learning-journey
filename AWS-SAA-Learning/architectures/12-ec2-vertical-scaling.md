# Vertical Scaling (Scale Up / Down)

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
  - Used when **application can't be easily distributed** across multiple instances.

---

## Quick Exam Checklist – Vertical Scaling (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Vertical scaling** | Change instance size; requires reboot/downtime. |

---

## Important Exam Points – Vertical Scaling

- **Vertical scaling** = **downtime** (stop → change type → start); **horizontal** = **ASG + stateless + session store**.