## 1. Amazon VPC – Core Foundations

### What a VPC Is

* Logically isolated virtual network inside AWS
* You control **IP ranges, routing, security, and connectivity**
* Regional construct (spans AZs)

### CIDR Planning (CRITICAL FOR EXAM)

* IPv4 CIDR range: **/16 to /28**
* Must NOT overlap if peering / TGW / hybrid is required
* Cannot resize a CIDR → must **add secondary CIDR**

**Exam trap**: Overlapping CIDRs = no peering, no TGW routing

---

## 2. Subnets & IP Addressing

### Subnets

* AZ-specific (cannot span AZs)
* Public or Private determined by **route table**, not subnet itself

### Reserved IPs (Know by heart)

AWS reserves **5 IPs per subnet**:

1. Network address
2. VPC router
3. DNS
4. Reserved future use
5. Broadcast (unused but reserved)

**Exam math tip**: /27 = 32 IPs → 27 usable

---

## 3. Internet Access Models

### Internet Gateway (IGW)

* Enables **IPv4 public internet access**
* One IGW per VPC
* Required for public subnets

### Public Subnet Requirements

* Route to IGW (0.0.0.0/0)
* Instance has **public IPv4 or Elastic IP**

### NAT Gateway vs NAT Instance (EXAM FAVORITE)

**NAT Gateway**

* Managed, scalable, AZ-specific
* Requires Elastic IP
* Supports up to 45 Gbps

**NAT Instance**

* EC2-based, manual scaling
* Requires disabling source/dest check
* Used only for legacy or cost edge cases

**Exam rule**: Default answer = **NAT Gateway**

---

## 4. Route Tables & Routing Logic

### Route Tables

* Control traffic direction
* One main route table per VPC
* Subnets can be explicitly associated

### Routing Priority

* Most specific route wins (longest prefix)

**Exam trap**: Route table misassociation causes "no internet" issues

---

## 5. Security Layers – NACL vs Security Groups

### Security Groups

* Stateful
* Allow rules only
* Attached to ENIs / instances
* Default deny

### Network ACLs

* Stateless
* Allow + Deny rules
* Subnet-level
* Evaluated in rule number order

**Exam shortcut**

* Instance-level → Security Group
* Subnet-wide control → NACL

---

## 6. VPC Connectivity Options

### VPC Peering

* One-to-one
* No transitive routing
* Same or cross-account
* Same or cross-region

**Exam trap**: A ↔ B ↔ C does NOT work

### Transit Gateway (TGW)

* Hub-and-spoke networking
* Supports **transitive routing**
* Scales to thousands of VPCs
* Centralized inspection & routing

**Exam signal words**

* "Global network"
* "Many VPCs"
* "Central routing"

---

## 7. Hybrid Connectivity (On-Prem ↔ AWS)

### Site-to-Site VPN

* IPSec over public internet
* Fast to deploy
* Variable latency

### Direct Connect (DX)

* Private, dedicated link
* Consistent latency
* Higher throughput

### VPN + DX (BEST PRACTICE)

* VPN for encryption
* DX for performance
* VPN acts as backup

**Exam default**: DX + VPN = correct for production hybrid

---

## 8. Advanced VPC Networking

### VPC Endpoints (HIGH EXAM WEIGHT)

**Gateway Endpoints**

* S3, DynamoDB only
* Route-table based
* Free

**Interface Endpoints (PrivateLink)**

* ENI-based
* Uses Private IPs
* Supports most AWS services

**Exam clue**: "No internet", "private access" → VPC Endpoint

---

## 9. DNS, Route 53 & Hybrid DNS

### Route 53 Basics

* Public hosted zones → internet
* Private hosted zones → VPC only

### Hybrid DNS Patterns

* Route 53 Resolver inbound endpoint
* Route 53 Resolver outbound endpoint

**Exam trigger**: "Resolve on-prem DNS from AWS"

---

## 10. Load Balancing & Networking

### ALB

* Layer 7
* HTTP/HTTPS
* Path & host routing

### NLB

* Layer 4
* Ultra-high performance
* Static IPs

**Exam rule**

* HTTP logic → ALB
* Performance / TCP → NLB

---

## 11. IPv6 in VPC

* IPv6 is always public
* No NAT for IPv6
* Uses Egress-Only IGW for outbound-only

**Exam trap**: NAT Gateway does NOT work for IPv6

---

## 12. VPC Flow Logs & Monitoring

* Capture traffic metadata
* Subnet / ENI / VPC level
* Used for troubleshooting & security

**Exam trigger**: "Why traffic is blocked?" → Flow Logs

---

## 13. Migration-Relevant Networking Decisions

### Lift-and-Shift

* Match on-prem IP ranges
* Use VPN or DX

### Hybrid Active-Active

* TGW + DX
* Central routing

### Internet-facing workloads

* Public subnet + ALB

---

## 14. VERY IMPORTANT SAA-C03 EXAM TOPICS

### Must-Know Cold

* CIDR sizing & reserved IP math
* NAT Gateway vs NAT Instance
* Security Group vs NACL
* VPC Peering limitations
* Transit Gateway use cases
* Gateway vs Interface Endpoints
* DX vs VPN vs DX+VPN

### Common Exam Traps

* Overlapping CIDRs
* Expecting transitive peering
* Forgetting IGW route
* Using NAT for IPv6

### Default Exam Answers

* Central networking → Transit Gateway
* Private AWS service access → VPC Endpoint
* Hybrid production → DX + VPN
* Internet egress from private subnet → NAT Gateway

---

## 15. One-Line Decision Guide (EXAM GOLD)

* Many VPCs → TGW
* Private S3 access → Gateway Endpoint
* No public internet allowed → Interface Endpoint
* Hybrid + encryption → VPN
* Hybrid + performance → DX
* Both → DX + VPN

---
