Networking Foundations (BGP, VPN, Direct Connect)

---

## 1. Border Gateway Protocol (BGP)

### Autonomous Systems (AS)

* An **Autonomous System (AS)** is a collection of routers managed by a single entity.
* Each AS represents a distinct routing domain in BGP.

### Autonomous System Numbers (ASN)

* ASNs are **globally unique** and allocated by **IANA**.
* ASN range: `0–65535`
* **Private ASNs**: `64512–65534`

### BGP Characteristics

* Operates over **TCP port 179**
* Reliable protocol (uses TCP)
* **Not automatic** – peers must be manually configured
* **Path-vector protocol**

  * Exchanges the *best path* to a destination
  * The path is represented by **ASPATH**

### BGP Types

* **iBGP (Internal BGP)**

  * Routing *within* a single AS
* **eBGP (External BGP)**

  * Routing *between* different ASes

---

## 2. IPsec VPN Fundamentals

### What is IPsec?

* IPsec is a **suite of protocols**
* Creates **secure tunnels** over **insecure networks**
* Operates between **two peers**

  * Local peer
  * Remote peer

### Security Features

* **Authentication**
* **Encryption**

---

## 3. Encryption Concepts (IPsec Context)

### Symmetric Encryption

* Fast and efficient
* Difficult to exchange keys securely

### Asymmetric Encryption

* Slower
* Easy and secure **public key exchange**

---

## 4. IPsec Phases (IKE)

### IPsec has Two Main Phases

#### IKE Phase 1 (Slow & Heavy)

* Authenticates peers using:

  * Pre-shared key (password), or
  * Certificates
* Uses **asymmetric encryption**
* Establishes a **shared symmetric key**
* Creates **IKE Security Association (IKE SA)**
* Results in **Phase 1 tunnel**

#### IKE Phase 2 (Fast & Agile)

* Uses keys created in Phase 1
* Negotiates:

  * Encryption algorithms
  * Keys for bulk data transfer
* Creates **IPsec Security Association (IPsec SA)**
* Results in **Phase 2 tunnel**
* Runs *over* Phase 1

---

## 5. VPN Types

### Policy-Based VPN

* Traffic matched using **rule sets**
* Each rule creates a **pair of SAs**
* Allows different security settings per rule

### Route-Based VPN

* Traffic matched using **network prefixes**
* Uses **a single pair of SAs**
* Preferred for scalability and dynamic routing (BGP)

---

## 6. AWS Site-to-Site VPN

### Definition

* A **logical encrypted connection** between:

  * AWS VPC
  * On-premises network
* Uses **IPsec**
* Runs over the **public internet**

### Components

* **Virtual Private Gateway (VGW)** – AWS side
* **Customer Gateway (CGW)** – On-premises side
* **VPN Connection** – Between VGW and CGW

### Characteristics

* Can be **highly available** if designed correctly
* **Quick to provision** (usually < 1 hour)

---

## 7. VPN Considerations

### Performance

* Throughput limit: ~**1.25 Gbps**
* Latency: **inconsistent** (public internet)

### Cost

* Hourly AWS VPN cost
* Data transfer (GB out)
* Possible on-premises data caps

### Operational

* Setup time: **hours**
* Software-based configuration
* Can be:

  * Used **with Direct Connect**
  * Used as a **backup for Direct Connect**

---

## 8. AWS Direct Connect (DX)

### What is Direct Connect?

* **Dedicated physical network connection** to AWS
* Bandwidth options:

  * 1 Gbps
  * 10 Gbps
  * 100 Gbps

### Connectivity Path

```
Customer Premises → DX Location → AWS Region
```

### Key Concepts

* **DX Location**: AWS-approved colocation facility
* **Port allocation** at DX location
* Charged for:

  * Port hours
  * Outbound data transfer

### Benefits

* **Low and consistent latency**
* **High bandwidth**
* Traffic does **NOT traverse the public internet**
* Can access:

  * AWS Private Services (VPCs)
  * AWS Public Services

---

## 9. Direct Connect Resilience (Important)

### Default Resilience: NONE

* Single points of failure:

  * DX location
  * DX router
  * Cross-connect
  * Customer router
  * Customer premises

### Key Notes

* AWS regions have **multiple DX locations**
* DX locations connect to AWS regions using **redundant high-speed links**
* By default:

  * One cross-connect
  * One customer/provider router
* Resilience must be **explicitly designed**

---

## 10. VPN vs Direct Connect (Quick Comparison)

| Feature    | Site-to-Site VPN | Direct Connect      |
| ---------- | ---------------- | ------------------- |
| Transport  | Public Internet  | Private Network     |
| Encryption | Yes (IPsec)      | Optional            |
| Speed      | ~1.25 Gbps       | Up to 100 Gbps      |
| Latency    | Variable         | Low & consistent    |
| Setup Time | Minutes–Hours    | Weeks               |
| Cost       | Lower            | Higher              |
| Resilience | Software-based   | Must be architected |

---


## One-Line Memory Hooks

* **BGP = manual, TCP-based routing between networks (AS → ASPATH)**
* **IPsec = secure tunnels over the public internet**
* **IKE Phase 1 builds trust, Phase 2 moves data**
* **Route-based VPNs scale and support BGP**
* **Site-to-Site VPN = fast, software, internet-based**
* **Direct Connect = private, physical, low-latency**
* **Direct Connect has no HA unless you design it**

---

## High-Value Exam Triggers

* **“Dynamic routing / ASPATH / TCP 179” → BGP**
* **“Encrypted tunnel over internet” → IPsec VPN**
* **“Needs BGP over VPN” → Route-based VPN**
* **“Quick hybrid connectivity” → Site-to-Site VPN**
* **“Consistent latency / no internet” → Direct Connect**
* **“High availability required” → Multiple DX + VPN backup**

---


