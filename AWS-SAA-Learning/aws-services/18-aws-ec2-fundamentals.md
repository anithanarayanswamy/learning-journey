# AWS EC2 Fundamentals

EC2 is an Infrastructure as a Service (IaaS) that provides access to virtual machines called Instances. Each instance is an operating system configured in certain ways. It uses default VPC networking, is AZ resilient, and an instance fails if the AZ fails. On-demand billing per second, local on-host storage or EBS. Storage will still be used even when the Instance is in STOPPED state and there will be storage charges. In TERMINATED state, no charges are applied as the whole instance will be deleted.

---

## Quick Exam Checklist – AWS EC2 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **EC2** | IaaS; VMs; AZ-resilient; on-demand per second; EBS persists when STOPPED. |

---

## Important Exam Points – AWS EC2

- **EC2**: Instance in one AZ; EBS in one AZ; storage billed when STOPPED; no charge when TERMINATED.