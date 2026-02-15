# Capacity Reservations & On-Demand Capacity Reservations

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

## Quick Exam Checklist – Capacity Reservations (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Capacity Reservation** | On-Demand = capacity guarantee, no discount; Zonal RI = discount + capacity. |

---

## Important Exam Points – Capacity Reservations

- **On-Demand Capacity Reservation** = **capacity guarantee** in AZ, **no discount**, **On-Demand pricing**.
- **Regional RI** = **discount only**, **no capacity guarantee**.
- **Zonal RI** = **discount + capacity reservation**.