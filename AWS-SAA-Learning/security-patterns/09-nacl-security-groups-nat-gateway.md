## NACL (Network Access Control List)

- **What it is**: Subnet-level firewall.
- **Stateless**: Request and response are evaluated separately (you must allow both directions).
- **Scope**: Only impacts traffic **crossing the subnet boundary**.
- **Rules**: **Explicit allow + explicit deny** supported.
- **Matches**: IP/CIDR, ports, protocols (no “logical” targets like Security Groups).
- **Associations**:
  - **A subnet can have only one NACL** (default or custom).
  - **A NACL can be associated with many subnets**.
- **Why it’s useful**: Pair with Security Groups when you need **explicit deny** (e.g., block known bad IP ranges).

## VPC Security Groups (SG)

- **What it is**: ENI-level (instance/network interface) firewall.
- **Stateful**: If you allow a request in one direction, the return traffic is automatically allowed.
- **Rules**: **Allow only** (no explicit deny; everything else is implicit deny).
- **Targets**: IP/CIDR + logical resources (other SGs, and self-reference).
- **Attachment**: Attached to **ENIs**, even if the console makes it look like it’s attached to an instance.
- **Scaling trick (exam-friendly)**:
  - **SG as a source** = “anything with that SG attached”.
  - **Self-reference** = “anything with this SG attached” (good for clustered apps; membership scales as instances are added/removed).

## NAT (Network Address Translation) basics

- **Purpose**: Remap source/destination IPs (most commonly **source NAT**) so private resources can access the internet.
- **IP masquerading**: Many private IPs “hide behind” one public IPv4 (helps with IPv4 scarcity).
- **Common goal**: Give instances in **private subnets** outbound internet access (patching, package downloads, calling public APIs).

## NAT Gateway (NATGW)

- **Where it lives**: Placed in a **public subnet**.
- **Public IP**: Uses an **Elastic IP (static public IPv4)**.
- **Availability model**: **AZ-resilient** (highly available within one AZ).
- **Scaling**: Managed service; scales (commonly referenced limit: up to **45 Gbps**).
- **Cost model**: **Hourly** + **data processed** charges.

### Required routing (high-yield)

- **Private subnet route table**: `0.0.0.0/0 -> NAT Gateway`
- **Public subnet route table (where NATGW sits)**: `0.0.0.0/0 -> Internet Gateway (IGW)`

### HA design 

- **Best practice**: Deploy **one NATGW per AZ** and point each AZ’s private subnets to the **NATGW in the same AZ** (avoids cross-AZ dependency and cross-AZ data charges).

### NATGW limitations (very exam-relevant)

- **No Security Groups** on the NATGW itself.
- **No port forwarding** (not supported).
- **Not a bastion host** (you can’t SSH to it; no OS access).
- **Does not work with IPv6**.

## IPv6 note: no NAT required

- **All IPv6 addresses are publicly routable** in AWS.
- For IPv6:
  - **Bidirectional internet**: `::/0 -> IGW`
  - **Outbound-only**: `::/0 -> Egress-only Internet Gateway`

## NAT Instance vs NAT Gateway (SAA‑C03)

### NAT Gateway (managed)

- **Availability**: Highly available within an AZ; use one per AZ for multi-AZ.
- **Bandwidth/Performance**: Scales automatically; optimized for NAT traffic.
- **Maintenance**: AWS-managed (no patching).
- **Type/size**: No instance sizing decisions.
- **Security Groups**: **Cannot** be attached.
- **NACL / Flow logs**: Use NACLs and VPC Flow Logs as usual (subnet/VPC features).
- **Port forwarding / Bastion**: **Not supported**.

### NAT Instance (EC2 you manage)

- **Availability**: You must design failover yourself (scripts/automation).
- **Bandwidth/Performance**: Depends on instance type/size and your configuration.
- **Maintenance**: You patch/upgrade and operate it.
- **Type/size**: You choose instance type based on workload.
- **Security Groups**: **Supported** (you attach SGs to the NAT instance and control traffic that way).
- **Port forwarding / Bastion**: Possible with custom configuration (can act as a bastion), but you own the risk/ops.

### NAT instance “gotchas” 

- Must **disable Source/Destination Check** on the NAT instance.
- Must be in a **public subnet** with a route to the **IGW**.
- Private subnet route table points `0.0.0.0/0` to the **NAT instance** (instead of NATGW).

## Important points (quick hits)

- **Private subnet needing outbound IPv4 internet**: NATGW (or NAT instance) + correct routing.
- **NATGW is per-AZ**: “Highly available” does **not** mean “region-wide”—use one per AZ for HA.
- **If you need private access to AWS services without internet/NAT**: consider **VPC endpoints**:
  - **Gateway endpoints**: S3, DynamoDB
  - **Interface endpoints (PrivateLink)**: many other services
- **IPv6**: No NAT; use **Egress-only IGW** for outbound-only IPv6.

---

## Quick Exam Checklist – Day 12 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **NACL** | Subnet-level; stateless (allow inbound + outbound); explicit allow/deny; IP/port/protocol. |
| **Security Group** | ENI-level; stateful; allow only; SG as source = scalable; self-reference for clusters. |
| **NAT** | Private → internet; source NAT; NATGW in public subnet. |
| **NAT Gateway** | Public subnet; Elastic IP; AZ-resilient; no SG; no port forward; no bastion; no IPv6. |
| **Private subnet route** | 0.0.0.0/0 → NAT Gateway; public subnet (NATGW) → 0.0.0.0/0 → IGW. |
| **HA NAT** | One NATGW per AZ; private subnets point to NATGW in same AZ. |
| **NAT Instance** | EC2; disable source/dest check; SG supported; can do port forward/bastion. |
| **VPC Endpoints** | Gateway (S3, DynamoDB); Interface (PrivateLink) for other services. |
| **IPv6** | No NAT; ::/0 → IGW (bidirectional) or Egress-only IGW (outbound only). |

---

## Important Exam Points – Day 12

- **NACL** = **stateless** (allow both directions); **Security Group** = **stateful** (return traffic auto-allowed).
- **NAT Gateway**: In **public subnet**; **no Security Groups**; **no port forwarding**; **not a bastion**; **no IPv6**.
- **Private subnet outbound**: Route **0.0.0.0/0 → NAT Gateway**; NATGW's subnet needs **0.0.0.0/0 → IGW**.
- **HA**: Use **one NATGW per AZ**; point each AZ's private subnets to **NATGW in same AZ** (avoid cross-AZ cost).
- **NAT Instance**: Disable **Source/Destination Check**; in public subnet; route private → NAT instance.
- **VPC Endpoints**: **Gateway** (S3, DynamoDB, free); **Interface** (PrivateLink, many services, per AZ).
- **IPv6**: Use **Egress-only Internet Gateway** for outbound-only IPv6 (no NAT).


