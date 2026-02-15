# EC2 Purchase Options – Reserved Instances (RI)

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
  - If you **don't launch instances** that match the RI, you are **still billed** for the reservation.
  - **Larger instances** may be **partially covered** by multiple smaller RIs (depending on RI type and normalization factors).

- **When to use**
  - **Steady-state**, predictable workloads.
  - Applications running **24/7** for **long term**.

- **Important exam points (RIs)**
  - Commit for **1 or 3 years** for **discounted pricing** compared to On-Demand.
  - **Unused RIs still incur charges** – discount applies only if you run matching instances.
  - **Zonal RI** = **discount + capacity reservation**. **Regional RI** = **discount only**.

---

## Quick Exam Checklist – Reserved Instances (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Reserved** | 1 or 3 year; discount; Regional = discount only; Zonal = discount + capacity reservation. |

---

## Important Exam Points – Reserved Instances

- **Regional RI** = **discount only**; **Zonal RI** = **discount + capacity reservation**; **unused RI** still billed.