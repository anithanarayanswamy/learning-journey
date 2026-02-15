# Horizontal Scaling (Scale Out / In)

**Horizontal scaling** means **adding or removing instances** behind a load balancer.

- **Characteristics**
  - Typically uses an **Auto Scaling Group (ASG)** to add/remove instances.
  - Requires **stateless** application or **off-host session storage** (e.g., ElastiCache, DynamoDB, RDS).
  - **No disruption** to the application when scaling if properly designed.
  - **No theoretical upper limit** – can keep adding more instances.

- **Pros**
  - Often **more cost-effective**: use many **smaller instances** instead of one big one.
  - **Granular scaling** – scale by small increments.
  - Provides **high availability** and **fault tolerance** across AZs.

- **Cons**
  - Requires **application design changes** (stateless, external session storage, etc.).

- **Important exam points (Horizontal Scaling)**
  - Requires **session management** – use **sticky sessions** or **external session store**.
  - Natural fit with **Elastic Load Balancing** and **Auto Scaling Groups**.

---

## Quick Exam Checklist – Horizontal Scaling (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Horizontal scaling** | Add/remove instances; ASG; stateless + session store (e.g. ElastiCache). |

---

## Important Exam Points – Horizontal Scaling

- **Horizontal scaling** = **ASG + stateless + session store** (e.g. ElastiCache, DynamoDB).