# EC2 Purchase Options – Spot

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

## Quick Exam Checklist – Spot (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Spot** | Up to 90% discount; can be interrupted (2-min notice); batch, stateless, fault-tolerant. |

---

## Important Exam Points – Spot

- **Spot**: **90% discount**; **2-minute** interruption notice; use for **batch, stateless, fault-tolerant**.