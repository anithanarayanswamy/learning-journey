# 1. EC2 Fundamentals

## What is EC2?

* Elastic Compute Cloud – resizable virtual servers in AWS
* Infrastructure as a Service (IaaS)
* Pay per second (Linux) / per hour (some Windows)

## Core Components

* AMI (Amazon Machine Image)
* Instance Type (CPU, memory, storage, network)
* EBS (block storage)
* Security Groups
* Key Pair
* ENI (Elastic Network Interface)

---

# 2. EC2 Instance Types (Know the Families)

## General Purpose

* Balanced compute, memory, networking
* Example: t3, t4g, m5
* Use: web servers, small DBs, dev/test

## Compute Optimized

* High CPU
* Example: c5, c6
* Use: batch processing, gaming servers, ML inference

## Memory Optimized

* High RAM
* Example: r5, x1
* Use: in-memory DB, caching

## Storage Optimized

* High disk throughput / IOPS
* Example: i3, d2
* Use: NoSQL DB, data warehousing

⚠️ Exam Tip:
If CPU-heavy → Compute Optimized
If RAM-heavy → Memory Optimized
If IOPS-heavy → Storage Optimized

---

# 3. EC2 Pricing Models (VERY IMPORTANT)

## On-Demand

* No commitment
* Highest cost
* Use for unpredictable workloads

## Reserved Instances (RI)

* 1 or 3 year commitment
* Significant discount
* Standard RI → No instance type change
* Convertible RI → Can change instance family

## Savings Plans

* Commit to $/hour usage
* More flexible than RIs

## Spot Instances

* Up to 90% cheaper
* Can be terminated anytime
* Use: fault-tolerant workloads, batch jobs

## Dedicated Hosts

* Physical server dedicated to you
* Used for compliance/licensing

⚠️ Exam Focus:

* Predictable workload → Reserved or Savings Plan
* Interruptible workload → Spot
* License-bound workload → Dedicated Host

---

# 4. AMIs (Amazon Machine Images)

## Types

* AWS provided
* Marketplace
* Custom AMI (created from EC2)

## Key Points

* Region-specific
* Can be copied across regions
* Backed by snapshots

⚠️ Exam Tip:
Multi-region deployment → Copy AMI to target region

---

# 5. EC2 Storage

# EBS (Elastic Block Store)

## Characteristics

* Network attached block storage
* Persistent
* AZ-specific

## Volume Types

* gp3 (General Purpose – default)
* io1/io2 (Provisioned IOPS)
* st1 (Throughput optimized HDD)
* sc1 (Cold HDD)

## Important Concepts

* Root volume can be EBS
* Snapshots stored in S3 (regionally)
* Can resize volume
* Encryption supported

⚠️ Exam Focus:

* Need high IOPS → io2
* Need general workload → gp3
* Snapshots are incremental

---

# Instance Store (Ephemeral Storage)

* Physically attached disk
* Data lost on stop/terminate
* High performance

Use: temporary data, cache, buffers

---

# EFS (Elastic File System)

* NFS file system
* Multi-AZ
* Shared across instances
* Auto scaling storage

Use: shared Linux workloads

---

# 6. Security in EC2

# Security Groups (Stateful)

* Instance-level firewall
* Allow rules only
* Return traffic automatically allowed

# NACLs (Stateless)

* Subnet-level
* Allow AND deny rules
* Evaluated in order

⚠️ Exam Difference:
Security Group = Stateful
NACL = Stateless

---

# 7. ENI (Elastic Network Interface)

* Virtual network card
* Has private IP
* Can have multiple IPs
* Can move between instances

Use: failover scenarios

---

# 8. Auto Scaling (High Importance)

## Auto Scaling Group (ASG)

* Maintains desired capacity
* Health checks
* Replaces unhealthy instances

## Scaling Types

* Dynamic scaling
* Scheduled scaling
* Predictive scaling

## Scaling Metrics

* CPU utilization
* ALB request count

⚠️ Exam Focus:
High availability → Multi-AZ ASG

---

# 9. Load Balancers (Closely Tied to EC2)

## ALB (Layer 7)

* HTTP/HTTPS
* Path-based routing
* Host-based routing

## NLB (Layer 4)

* TCP/UDP
* High performance
* Static IP

## Target Groups

* EC2
* IP
* Lambda

⚠️ Exam Tip:
Microservices routing → ALB
Ultra-low latency → NLB

---

# 10. Placement Groups

## Cluster

* Low latency
* Same AZ

## Spread

* Across distinct hardware
* High availability

## Partition

* Large distributed workloads

⚠️ Exam Focus:
HPC → Cluster
Max HA → Spread

---

# 11. EC2 Lifecycle

States:

* Pending
* Running
* Stopping
* Stopped
* Terminated

⚠️ Important:
Stopping → EBS persists
Terminating → EBS deleted (unless configured otherwise)

---

# 12. Elastic IP

* Static public IPv4
* Region-specific
* Charged if unused

---

# 13. IAM Roles for EC2

* Attach role to instance
* No need to store credentials
* Used for S3, DynamoDB access

⚠️ Exam Best Practice:
Never store access keys on EC2

---

# 14. Monitoring

## CloudWatch

* Basic monitoring (5 min)
* Detailed monitoring (1 min)

## Status Checks

* System status check
* Instance status check

---

# 15. EC2 Hibernate

* Saves RAM to EBS
* Faster restart than stop
* Only supported on some instance types

---

# 16. High Availability Patterns

* Multi-AZ deployment
* Auto Scaling + ALB
* Use EFS for shared storage

---

# 17. Common Exam Scenarios

1. Cost optimization → Use Spot or Savings Plans
2. Shared file system → Use EFS
3. High IOPS DB → io2 EBS
4. Temporary cache → Instance Store
5. Web tier scaling → ASG + ALB
6. Need static public IP → Elastic IP
7. Secure access to AWS services → IAM Role

---

# VERY IMPORTANT EC2 EXAM TOPICS

⭐ Pricing models comparison
⭐ EBS vs Instance Store
⭐ Security Group vs NACL
⭐ Auto Scaling behavior
⭐ ALB vs NLB
⭐ Multi-AZ high availability design
⭐ IAM roles for EC2
⭐ Spot instance behavior
⭐ EBS snapshot behavior
⭐ Placement groups

---
