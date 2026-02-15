# VPC Routing & Internet Gateway

Demo lesson: Implementing an Internet Gateway, Route Tables and Routes within the Animals4Life VPC to support the WEB public subnets. Once the WEB subnets are public, we create a bastion host with public IPv4 addressing and connect to it to test. At the end of this demo, you will have a fully working public-capable VPC and bastion ingress point.

**Note**: This lesson builds upon the subnets created in [Day-10.md](./Day-10.md). We use the previously created WEB subnets (sn-web-A, sn-web-B, sn-web-C) to configure routing and internet connectivity.

---

## VPC Router Fundamentals

### What is a VPC Router?

- Every VPC has a **VPC router** - it's highly available and managed by AWS
- The router exists at the **network+1 address** in every subnet
  - Example: For subnet 10.16.48.0/20, the router is at 10.16.48.1
- The VPC router **routes traffic between subnets** within the VPC
- Routing is controlled by **Route Tables** - each subnet has one route table
- A VPC has a **main route table** - this is the default route table for new subnets

### How VPC Routing Works

- **Intra-VPC routing**: Traffic between subnets in the same VPC is automatically routed
- **Route table lookup**: When a packet leaves a subnet, the route table determines the next hop
- **Longest prefix match**: Route tables use longest prefix matching to determine routes
- **Default local route**: All subnets in a VPC can communicate via the implicit local route

**Important Points:**
- ✅ VPC router is **highly available** - no single point of failure
- ✅ Router is at **network+1 address** (e.g., 10.16.48.1 for 10.16.48.0/20)
- ✅ **Route tables control routing** - not the router itself
- ✅ One subnet = **one route table**, but one route table can have **multiple subnets**
- ✅ **Main route table** is the default for subnets without explicit association

---

## Route Tables Explained

### Route Table Basics

- **Route tables** define how traffic is routed from subnets
- Each subnet **must** be associated with exactly one route table
- A route table can be associated with **multiple subnets**
- If a subnet is not explicitly associated with a route table, it uses the **main route table**

### Route Table Components

1. **Local Route**: Automatically added for VPC CIDR (cannot be modified or deleted)
   - Example: `10.16.0.0/16 → local`
   - Allows communication between subnets in the VPC

2. **Internet Gateway Route**: Routes traffic to the internet
   - Example: `0.0.0.0/0 → igw-xxxxx` (IPv4)
   - Example: `::/0 → igw-xxxxx` (IPv6)
   - Makes a subnet **public**

3. **NAT Gateway Route**: Routes traffic to NAT Gateway for outbound internet
   - Example: `0.0.0.0/0 → nat-xxxxx`
   - Used in **private subnets** that need outbound internet access

4. **VPC Peering Route**: Routes traffic to peered VPCs
   - Example: `10.20.0.0/16 → pcx-xxxxx`

5. **VPC Endpoint Route**: Routes traffic to AWS services via private link
   - Example: `pl-xxxxx → vpce-xxxxx`

### Route Table Types

- **Main Route Table**: Default route table created with VPC
- **Custom Route Table**: Created explicitly for specific subnets
- **Explicit Association**: Subnet explicitly associated with a route table
- **Implicit Association**: Subnet uses main route table (no explicit association)

**Important Points:**
- ✅ **One subnet = one route table** (but route table can have multiple subnets)
- ✅ **Main route table** is default for subnets without explicit association
- ✅ **Local route** is automatic and cannot be deleted
- ✅ **Longest prefix match** determines which route is used
- ✅ Route tables are **region-specific** (not AZ-specific)
- ✅ You can have **multiple route tables** in one VPC

---

## Internet Gateway (IGW)

### What is an Internet Gateway?

- An **Internet Gateway** is a region-resilient gateway attached to a VPC
- It provides **bidirectional connectivity** between the VPC and the internet
- It also provides connectivity to **AWS public zone services** (S3, SQS, SNS, etc.)
- It's a **managed service** - AWS handles performance, scaling, and availability
- The IGW runs from within the **AWS public zone**

### IGW Characteristics

- **1 VPC = 0 or 1 IGW** (a VPC can have zero or one IGW)
- **1 IGW = 0 or 1 VPC** (an IGW can be attached to zero or one VPC)
- **Region-resilient**: Automatically scales and is highly available
- **No bandwidth charges**: No data transfer charges for using IGW
- **IPv4 and IPv6 support**: Supports both IP versions

### How IGW Works

1. **Attachment**: IGW must be **attached** to a VPC (not just created)
2. **Route Table Configuration**: Route tables must have routes pointing to IGW
3. **Public IP Mapping**: IGW maintains a mapping between public and private IPs
4. **Traffic Flow**: 
   - **Outbound**: Instance with private IP → IGW translates to public IP → Internet
   - **Inbound**: Internet → IGW translates public IP → private IP → Instance

### Public IPv4 Address Mechanism

**CRITICAL UNDERSTANDING**: Public IPv4 addresses **never touch the actual services** inside the VPC. Here's how it works:

1. When you allocate a public IPv4 address to an EC2 instance, **a record is created** that the IGW maintains
2. The IGW **links the private IP to the public IP** in its mapping table
3. The instance itself is **NOT configured with the public IP** in the OS
4. That's why when you check the IP address inside the EC2 instance OS, it **only shows the private IP**
5. The IGW performs **NAT (Network Address Translation)** at the gateway level

**Example Flow:**
- EC2 instance has private IP: `10.16.48.50`
- EC2 instance has public IP: `54.123.45.67`
- Inside the OS: Only `10.16.48.50` is visible
- Internet sees: `54.123.45.67`
- IGW translates between them automatically

**Important Points:**
- ✅ **1 VPC = 0 or 1 IGW** (cannot have multiple IGWs per VPC)
- ✅ **1 IGW = 0 or 1 VPC** (one-to-one relationship when attached)
- ✅ IGW must be **attached** to VPC (creation alone is not enough)
- ✅ **Public IPs are not configured in the OS** - only private IPs are visible
- ✅ IGW performs **NAT translation** at the gateway level
- ✅ IGW is **region-resilient** and highly available
- ✅ **No bandwidth charges** for IGW usage
- ✅ IGW supports **both IPv4 and IPv6**
- ✅ IGW provides access to **AWS public zone services** (S3, SQS, SNS)

---

## Making Subnets Public

### What Makes a Subnet Public?

A subnet becomes **public** when:
1. It has a **route to an Internet Gateway** in its route table
   - Route: `0.0.0.0/0 → igw-xxxxx` (IPv4)
   - Route: `::/0 → igw-xxxxx` (IPv6)
2. Instances in the subnet can be assigned **public IPv4 addresses** (optional but common)
3. The subnet has **auto-assign public IPv4** enabled (optional)

### Steps to Make a Subnet Public

1. **Create and Attach IGW** to the VPC
2. **Create a Route Table** (or use existing) for public subnets
3. **Add Route to IGW**: `0.0.0.0/0 → igw-xxxxx`
4. **Associate Route Table** with the subnet(s)
5. **Enable Auto-Assign Public IPv4** on the subnet (optional but recommended for public subnets)

**Important Points:**
- ✅ **Route to IGW** makes a subnet public (not just having IGW attached)
- ✅ **Auto-assign public IPv4** is a subnet-level setting
- ✅ Public subnet = route to IGW + (optionally) public IPs
- ✅ Private subnet = no route to IGW (or route to NAT Gateway for outbound only)

---

## Bastion Host / Jump Box

### What is a Bastion Host?

- A **bastion host** (also called **jump box**) is an EC2 instance in a **public subnet**
- It serves as a **secure entry point** to access private resources in the VPC
- **Incoming management connections** arrive at the bastion host
- From the bastion, you can then **access internal VPC resources** (private subnets)
- Often the **only way IN to a VPC** for administrative access

### Bastion Host Use Cases

1. **SSH/RDP Access**: Connect to private EC2 instances
2. **Database Access**: Access RDS instances in private subnets
3. **Management Tasks**: Run administrative commands on private resources
4. **Troubleshooting**: Debug issues in private subnets
5. **Compliance**: Meet security requirements for accessing private resources

### Bastion Host Best Practices

- **Place in public subnet** with route to IGW
- **Minimal attack surface**: Only necessary ports open (SSH port 22, RDP port 3389)
- **Security Groups**: Restrict access to specific IP addresses
- **Key Pairs**: Use SSH key pairs (not passwords)
- **Multi-AZ**: Deploy bastion hosts in multiple AZs for HA
- **Session Manager**: Consider using AWS Systems Manager Session Manager instead

**Important Points:**
- ✅ Bastion host = **EC2 instance in public subnet**
- ✅ Used for **secure access** to private resources
- ✅ **Only way IN** to VPC for many architectures
- ✅ Should have **restrictive security groups** (only allow specific IPs)
- ✅ Consider **AWS Systems Manager Session Manager** as alternative (no public IP needed)
- ✅ **Multi-AZ deployment** recommended for high availability

---

## Demo Implementation Summary

In this demo, we implemented routing and internet connectivity for the Animals4Life VPC:

### Steps Completed

1. **Used Previously Created Subnets**: 
   - Referenced the WEB subnets created in [Day-10.md](./Day-10.md):
     - `sn-web-A` (10.16.48.0/20, AZ-A)
     - `sn-web-B` (10.16.112.0/20, AZ-B)
     - `sn-web-C` (10.16.176.0/20, AZ-C)

2. **Created Internet Gateway**:
   - Created IGW in the VPC
   - Attached IGW to the Animals4Life VPC

3. **Created Route Table for Web Subnets**:
   - Created a custom route table for public web subnets
   - This route table is separate from the main route table

4. **Associated Route Table with Web Subnets**:
   - Associated the custom route table with all three WEB subnets (A, B, C)
   - This makes them use the custom route table instead of the main route table

5. **Added Default Routes to IGW**:
   - Added IPv4 default route: `0.0.0.0/0 → igw-xxxxx`
   - Added IPv6 default route: `::/0 → igw-xxxxx`
   - These routes make the subnets public

6. **Enabled Auto-Assign Public IPv4**:
   - Enabled auto-assign public IPv4 for `sn-web-A`
   - Enabled auto-assign public IPv4 for `sn-web-B`
   - Enabled auto-assign public IPv4 for `sn-web-C`
   - This ensures instances launched in these subnets get public IPs automatically

7. **Created Bastion Host**:
   - Launched EC2 instance in one of the public WEB subnets
   - Configured with public IPv4 addressing
   - Connected to it via SSH to test connectivity

### Result

At the end of this demo, you have:
- ✅ **Fully working public-capable VPC** with internet connectivity
- ✅ **Public WEB subnets** across three AZs (A, B, C)
- ✅ **Bastion host** as secure ingress point to the VPC
- ✅ **Route tables** properly configured for public internet access

---

## Key Points Summary

### VPC Router
- ✅ VPC router is **highly available** at network+1 address
- ✅ Routes traffic between subnets automatically
- ✅ Controlled by route tables

### Route Tables
- ✅ **One subnet = one route table** (but route table can have multiple subnets)
- ✅ **Main route table** is default for subnets without explicit association
- ✅ **Local route** is automatic and cannot be deleted
- ✅ **Longest prefix match** determines route selection
- ✅ Route tables are **region-specific**

### Internet Gateway
- ✅ **1 VPC = 0 or 1 IGW** (one-to-one relationship)
- ✅ Must be **attached** to VPC (not just created)
- ✅ **Public IPs are not in OS** - only private IPs visible
- ✅ IGW performs **NAT translation** at gateway level
- ✅ **Region-resilient** and highly available
- ✅ **No bandwidth charges** for IGW usage
- ✅ Supports **both IPv4 and IPv6**

### Public Subnets
- ✅ **Route to IGW** makes subnet public (`0.0.0.0/0 → igw`)
- ✅ **Auto-assign public IPv4** is subnet-level setting
- ✅ Public subnet = route to IGW + (optionally) public IPs

### Bastion Host
- ✅ **EC2 instance in public subnet**
- ✅ **Secure entry point** to private resources
- ✅ Should have **restrictive security groups**
- ✅ Consider **Session Manager** as alternative

### Common Scenarios

1. **Cannot access internet from instance**:
   - Check: Route table has `0.0.0.0/0 → igw-xxxxx`
   - Check: IGW is attached to VPC
   - Check: Security group allows outbound traffic
   - Check: Network ACL allows traffic

2. **Instance has public IP but cannot be reached**:
   - Check: Route table has route to IGW
   - Check: Security group allows inbound traffic
   - Check: Network ACL allows traffic
   - Check: Instance is in public subnet

3. **Private subnet needs internet access**:
   - Solution: Use NAT Gateway (not IGW)
   - Route: `0.0.0.0/0 → nat-xxxxx`
   - NAT Gateway must be in public subnet

4. **Multiple route tables in VPC**:
   - Each subnet can have different route table
   - Main route table is default
   - Custom route tables for specific subnets

---

## Quick Exam Checklist – Day 11 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **VPC Router** | At network+1; HA; route tables control routing. |
| **Route table** | One subnet = one route table; local route automatic; longest prefix match. |
| **IGW** | 1 VPC = 0 or 1 IGW; must attach; public IP not in OS (IGW does NAT). |
| **Public subnet** | Route 0.0.0.0/0 → igw; auto-assign public IPv4 optional. |
| **Bastion host** | EC2 in public subnet; secure entry to private resources; restrictive SG; consider Session Manager. |
| **Private + internet** | NAT Gateway in public subnet; private route 0.0.0.0/0 → nat. |

---

## Important Exam Points – Day 11

- **VPC router** at **network+1** (e.g. 10.16.48.1 for 10.16.48.0/20); **highly available**; controlled by **route tables**.
- **IGW**: **1 VPC = 0 or 1 IGW**; must **attach** to VPC; **public IP not configured in OS**—IGW does NAT at gateway.
- **Route to IGW** (0.0.0.0/0 → igw) makes subnet **public**; auto-assign public IPv4 is **subnet-level**.
- **Bastion** = EC2 in **public subnet**; only way in for many designs; use **Session Manager** to avoid public IP.
- **Private subnet outbound**: Use **NAT Gateway** (not IGW); route 0.0.0.0/0 → nat-xxx; NAT in **public** subnet.
- **Longest prefix match** determines which route is used; **main route table** = default for new subnets.

---

## Best Practices

1. **Route Table Organization**:
   - Use separate route tables for public and private subnets
   - Name route tables descriptively (e.g., "public-web-rt", "private-app-rt")

2. **Internet Gateway**:
   - Attach IGW only to VPCs that need internet connectivity
   - Use IGW for public subnets, NAT Gateway for private subnets

3. **Bastion Host**:
   - Deploy in multiple AZs for high availability
   - Use restrictive security groups (only allow specific IPs)
   - Consider AWS Systems Manager Session Manager

4. **Security**:
   - Database subnets should NOT have route to IGW
   - Use security groups and network ACLs together
   - Enable VPC Flow Logs for monitoring

5. **IPv6**:
   - If using IPv6, add `::/0 → igw` route for public subnets
   - Remember IPv6 addresses are publicly routable by default

---

## Common Mistakes to Avoid

1. ❌ **Creating IGW but not attaching it** - IGW must be attached to VPC
2. ❌ **Attaching IGW but not adding route** - Route table must have route to IGW
3. ❌ **Using IGW for private subnets** - Use NAT Gateway instead
4. ❌ **Expecting public IP in OS** - Public IPs are only visible via IGW mapping
5. ❌ **One route table for all subnets** - Use separate route tables for public/private
6. ❌ **Bastion host without security restrictions** - Always restrict access

---

## Next Steps

After completing this lesson, you should understand:
- How VPC routing works
- How to make subnets public using IGW
- How route tables control traffic flow
- How bastion hosts provide secure access
- The relationship between public IPs and private IPs

**Related Topics to Study**:
- NAT Gateway (for private subnet internet access)
- VPC Endpoints (for private AWS service access)
- Security Groups vs Network ACLs
- VPC Peering
- AWS Systems Manager Session Manager
