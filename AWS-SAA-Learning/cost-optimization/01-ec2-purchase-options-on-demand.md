# EC2 Purchase Options – On-Demand

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

## Quick Exam Checklist – On-Demand (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **On-Demand** | No commitment; per second; unpredictable/critical workloads; cannot be interrupted. |

---

## Important Exam Points – On-Demand

- **On-Demand**: **Unpredictable** or **cannot be interrupted**; default option; per second.