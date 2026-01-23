# Multi-Tier Subnet Design Implementation

Demo lesson: implementing the multi-tier subnet design for Animals4Life including IPv6 configuration for subnets

---

## Subnet Design Overview

This design implements a **three-tier architecture** (Web, Application, Database) across **three Availability Zones** for high availability and fault tolerance.

### Architecture Principles

- **Multi-AZ deployment**: Subnets distributed across AZ-A, AZ-B, and AZ-C
- **Tiered architecture**: Separate subnets for Web, Application, and Database layers
- **Reserved subnets**: Reserved for future use or management purposes
- **IPv6 support**: Each subnet configured with custom IPv6 values

---

## Subnet Configuration Table

| NAME | CIDR | AZ | Custom IPv6 Value |
|------|------|----|-------------------|
| sn-reserved-A | 10.16.0.0/20 | AZ-A | IPv6 00 |
| sn-db-A | 10.16.16.0/20 | AZ-A | IPv6 01 |
| sn-app-A | 10.16.32.0/20 | AZ-A | IPv6 02 |
| sn-web-A | 10.16.48.0/20 | AZ-A | IPv6 03 |
| sn-reserved-B | 10.16.64.0/20 | AZ-B | IPv6 04 |
| sn-db-B | 10.16.80.0/20 | AZ-B | IPv6 05 |
| sn-app-B | 10.16.96.0/20 | AZ-B | IPv6 06 |
| sn-web-B | 10.16.112.0/20 | AZ-B | IPv6 07 |
| sn-reserved-C | 10.16.128.0/20 | AZ-C | IPv6 08 |
| sn-db-C | 10.16.144.0/20 | AZ-C | IPv6 09 |
| sn-app-C | 10.16.160.0/20 | AZ-C | IPv6 0A |
| sn-web-C | 10.16.176.0/20 | AZ-C | IPv6 0B |

---

## Understanding Subnet Creation

### What is a Subnet?

- A **subnet** is a logical subdivision of a VPC's IP address range
- Each subnet must be associated with **exactly one Availability Zone**
- Subnets enable you to organize resources and control network traffic flow
- Subnets within the same VPC can communicate with each other by default

### CIDR Block Sizing (/20 Subnets)

- **/20 CIDR block** = 4,096 IP addresses (2^12)
- **Usable IPs**: 4,091 (after AWS reserved IPs)
- **Range calculation**: Each /20 block contains 16 /24 subnets worth of addresses
- **Example**: 10.16.0.0/20 covers 10.16.0.0 to 10.16.15.255

**Important :**
- Subnet CIDR must be a subset of the VPC CIDR
- Subnets **cannot overlap** with each other
- AWS reserves 5 IPs per subnet (first 4 + last 1)

---

## Multi-Tier Architecture Explained

### Web Tier Subnets
- **Purpose**: Host public-facing web servers, load balancers
- **Traffic**: Inbound from internet, outbound to application tier
- **Security**: Typically has internet gateway attached

### Application Tier Subnets
- **Purpose**: Host application servers, business logic
- **Traffic**: Receives from web tier, communicates with database tier
- **Security**: No direct internet access (private subnet)

### Database Tier Subnets
- **Purpose**: Host databases (RDS, self-managed databases)
- **Traffic**: Only from application tier
- **Security**: Most restricted, typically no internet gateway

**Important :**
- **Network ACLs** and **Security Groups** control traffic between tiers
- Database subnets should be in **private subnets** (no route to internet gateway)
- Use **NAT Gateway** in public subnet for outbound internet access from private subnets

---

## Availability Zone Distribution

### Why Multi-AZ?

- **High Availability**: If one AZ fails, resources in other AZs continue operating
- **Fault Tolerance**: Distributes risk across multiple physical locations
- **Load Distribution**: Can balance traffic across AZs

### AZ Best Practices

- Always deploy resources across **at least 2 AZs** for production
- **3 AZs** provides better redundancy and distribution
- Each tier should have subnets in **all AZs** for consistency

**Important :**
- **Subnets cannot span multiple AZs** — one subnet = one AZ
- Resources in different AZs can communicate via VPC routing
- Cross-AZ data transfer **incurs charges** (not free)

---

## IPv6 Configuration

### IPv6 in AWS VPC

- AWS supports **dual-stack** VPCs (IPv4 + IPv6)
- IPv6 addresses are **publicly routable** by default
- Each subnet can have its own IPv6 CIDR block
- Custom IPv6 values allow you to organize and identify subnets

### IPv6 Addressing

- **Format**: 2001:db8::/64 (example)
- **Custom values**: The hex values (00, 01, 02, etc.) help identify subnet purpose
- **Benefits**: 
  - Larger address space
  - Simplified addressing
  - Native internet connectivity

**Important :**
- IPv6 addresses are **globally unique** and **publicly routable**
- If you need private IPv6, use **egress-only internet gateway** (not NAT)
- IPv6 **does not use NAT** — addresses are directly routable

---

## Subnet Creation Steps

### 1. Plan Your CIDR Blocks
- Calculate required IP addresses per subnet
- Ensure no overlap between subnets
- Leave room for future growth

### 2. Create Subnets in AWS Console/CLI
- Navigate to VPC → Subnets → Create subnet
- Select VPC and Availability Zone
- Specify IPv4 CIDR block
- Optionally configure IPv6 CIDR

### 3. Configure Route Tables
- **Public subnets**: Route to Internet Gateway (0.0.0.0/0 → igw-xxx)
- **Private subnets**: Route to NAT Gateway for outbound (0.0.0.0/0 → nat-xxx)
- **Database subnets**: No default route to internet

### 4. Set Up Network ACLs (Optional)
- Network ACLs are **stateless** (must allow both inbound and outbound)
- Applied at subnet level
- Default: Allow all traffic

### 5. Configure Security Groups
- Security Groups are **stateful** (return traffic automatically allowed)
- Applied at instance level
- Default: Deny all inbound, allow all outbound

---

## Key Points 

### Subnet Fundamentals
- ✅ Subnet must be in **one AZ only** (cannot span AZs)
- ✅ Subnet CIDR must be **subset of VPC CIDR**
- ✅ Subnets **cannot overlap**
- ✅ AWS reserves **5 IPs per subnet** (first 4 + last 1)

### Public vs Private Subnets
- ✅ **Public subnet**: Has route to Internet Gateway (0.0.0.0/0 → igw)
- ✅ **Private subnet**: No route to Internet Gateway
- ✅ **Private subnet with NAT**: Route to NAT Gateway for outbound internet

### Routing
- ✅ **Route tables** control subnet routing
- ✅ One subnet can have **one route table** (but route table can have multiple subnets)
- ✅ **Default route table** is created with VPC
- ✅ **Main route table** is the default for new subnets

### Network ACLs vs Security Groups
- ✅ **Network ACLs**: Stateless, subnet-level, allow/deny rules
- ✅ **Security Groups**: Stateful, instance-level, allow rules only
- ✅ **Both** can be used together for defense in depth

### IPv6 Considerations
- ✅ IPv6 addresses are **publicly routable** by default
- ✅ Use **egress-only internet gateway** for private IPv6 (not NAT)
- ✅ IPv6 **does not use NAT**

### High Availability
- ✅ Deploy resources across **multiple AZs** for HA
- ✅ Use **Application Load Balancer** to distribute across AZs
- ✅ **RDS Multi-AZ** provides automatic failover

### Cost Optimization
- ✅ **Cross-AZ data transfer** is charged ($0.01/GB)
- ✅ **Same-AZ transfer** is free
- ✅ Consider **VPC endpoints** to avoid NAT Gateway costs for AWS services

---

## Best Practices

1. **Naming Convention**: Use descriptive names (tier-AZ format)
2. **CIDR Planning**: Leave room for future expansion
3. **Security**: Database subnets should be most restricted
4. **Monitoring**: Enable VPC Flow Logs for troubleshooting
5. **Documentation**: Document your subnet design and purpose

---

## Common Scenarios

### Scenario 1: Subnet Overlap Error
- **Problem**: Cannot create subnet due to CIDR overlap
- **Solution**: Choose non-overlapping CIDR blocks within VPC range

### Scenario 2: Cannot Access Internet from Private Subnet
- **Problem**: Instances in private subnet need internet access
- **Solution**: Add route to NAT Gateway in route table

### Scenario 3: Database Security
- **Problem**: Database should not be accessible from internet
- **Solution**: Place in private subnet with no internet gateway route

### Scenario 4: High Availability Setup
- **Problem**: Application must survive AZ failure
- **Solution**: Deploy across multiple AZs with load balancer