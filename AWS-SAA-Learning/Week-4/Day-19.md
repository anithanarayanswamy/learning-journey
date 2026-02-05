# ASG Lifecycle Hooks – SAA-C03 Notes

Auto Scaling Group (ASG) Lifecycle Hooks allow you to **pause instances during scaling events** so you can run **custom actions** before an instance is put into service or terminated.

### Core Concepts

* Lifecycle hooks allow **custom actions on instances during ASG actions**.
* Hooks can be attached to:

  * **Instance launch**
  * **Instance terminate**
* When a lifecycle hook is triggered:

  * The instance is placed into a **wait state**.
  * The ASG workflow is paused.

### Lifecycle Flow

* During **instance launch**:

  * Instance enters `Pending:Wait`.
* During **instance terminate**:

  * Instance enters `Terminating:Wait`.
* The instance remains paused:

  * Until the **lifecycle hook timeout** is reached, or
  * Until you explicitly resume the process using `CompleteLifecycleAction`.

### Timeout Behavior

* If the timeout expires:

  * **CONTINUE** → ASG proceeds with launch or termination.
  * **ABANDON** → ASG stops the action and instance is not put into service.

### Resuming the Process

* You must call **CompleteLifecycleAction** to:

  * Continue the ASG workflow, or
  * Abandon the lifecycle action.
* This is typically done by:

  * AWS Lambda
  * Automation scripts
  * External systems

### Notifications & Integration

* Lifecycle hooks can publish events to:

  * **Amazon EventBridge**
  * **Amazon SNS**
* Common integrations:

  * Lambda for automation
  * SQS for decoupled processing
  * Manual approval workflows

### Important Exam Points (Lifecycle Hooks)

* Lifecycle hooks **pause**, they do not replace scaling.
* Instances **remain billable** while waiting.
* Common use cases:

  * Install software before instance enters service.
  * Run configuration checks.
  * Drain connections before termination.
* Lifecycle hooks are **not health checks**.
* If the exam mentions:

  * *“Run custom logic before instance is added or removed”* → Lifecycle Hook.

---

# ASG Health Checks – SAA-C03 Notes

ASG health checks determine **when an instance should be replaced**.

### Health Check Types

* **EC2 (Default)**
* **ELB (Optional, must be enabled)**
* **Custom**

### EC2 Health Checks

* An instance is marked **UNHEALTHY** if it is:

  * Stopping
  * Stopped
  * Terminated
  * Shutting down
  * Impaired (not 2/2 status checks)
* This is **infrastructure-level only**.
* Does **not** check application health.

### ELB Health Checks

* An instance is **HEALTHY** when:

  * Instance is running, and
  * Passing the ELB health check.
* ELB checks are:

  * **Application-aware**
  * Layer 7 (HTTP/HTTPS) or Layer 4 (TCP)

### Custom Health Checks

* Health status is set by an **external system**.
* Useful when:

  * Health logic is complex.
  * You want full control over replacement decisions.

### Health Check Grace Period

* Default: **300 seconds**
* Purpose:

  * Delay health checks after launch.
  * Allow time for:

    * OS startup
    * Bootstrapping
    * Application initialization
* Prevents premature termination.

### Important Exam Points (ASG Health Checks)

* ASG + ALB → **Enable ELB health checks**.
* EC2 health checks ≠ application health.
* Grace period is critical for:

  * Large AMIs
  * Long user-data scripts
* If ELB marks instance unhealthy → ASG replaces it.
* If the question mentions:

  * *“Replace instances only when app fails”* → ELB health checks.

---

# SSL Offload – SAA-C03 Notes

SSL offloading controls **where encryption and decryption occur** in a load-balanced architecture.

---

## Bridging

* Listener configured for **HTTPS**.
* SSL connection is **terminated at the ELB**.
* ELB requires a **certificate** for the domain.
* ELB initiates a **new SSL connection** to backend instances.
* Backend instances:

  * Must have SSL certificates installed.
  * Perform cryptographic operations.

### Use Case

* End-to-end encryption required.
* Internal traffic must remain encrypted.

---

## Pass-through

* Listener configured for **TCP** (typically NLB).
* **No encryption or decryption** occurs at the load balancer.
* Traffic is passed directly to backend instances.
* Each instance must:

  * Handle SSL
  * Store certificates
* AWS never sees decrypted traffic.

### Use Case

* Strict compliance requirements.
* Full control over encryption.

---

## Offload

* Listener configured for **HTTPS**.
* SSL is terminated at the ELB.
* Backend connections use **HTTP**.
* Backend instances:

  * Do not need certificates.
  * Perform no cryptographic work.

### Use Case

* Most common and recommended architecture.
* Simplifies certificate management.
* Reduces backend CPU usage.

### Important Exam Points (SSL Offload)

* **Default best practice → SSL Offload**.
* Pass-through requires **NLB**, not ALB.
* Use **ACM** with ALB for certificate management.
* If exam mentions:

  * *“Reduce backend CPU usage”* → SSL Offload.
  * *“End-to-end encryption”* → Bridging.

---

# Connection Stickiness – SAA-C03 Notes

Connection stickiness controls **whether a client is consistently routed to the same backend instance**.

### Core Concepts

* Stickiness generates a **cookie**.
* The cookie locks a client to a **single backend instance**.
* Stickiness duration:

  * **1 second to 7 days**

### Without Stickiness

* Connections are distributed across:

  * All healthy, in-service instances.
* Ideal for:

  * Stateless applications.

### Risks of No Stickiness

* If the application stores state locally:

  * User logouts
  * Shopping cart losses
  * Session inconsistency

### Important Exam Points (Stickiness)

* Stickiness is an **anti-pattern** for scalability.
* Preferred solution:

  * Externalize state (DynamoDB, ElastiCache).
* Stickiness supported by:

  * ALB
  * Classic Load Balancer
* Stickiness can cause uneven load distribution.

---

# Gateway Load Balancer (GWLB) – SAA-C03 Notes

GWLB is designed to **deploy, scale, and manage third-party network appliances**.

### Core Purpose

* Run and scale **3rd-party appliances**, such as:

  * Firewalls
  * Intrusion Detection Systems (IDS)
  * Intrusion Prevention Systems (IPS)

### Traffic Handling

* Supports:

  * **Inbound traffic**
  * **Outbound traffic**
* Provides **transparent inspection and protection**.
* No application changes required.

### Architecture

* Traffic enters and leaves via **GWLB endpoints**.
* GWLB:

  * Distributes traffic across multiple backend appliances.
* Appliances can scale horizontally.

### Networking

* Traffic and metadata are tunneled using the **GENEVE protocol**.
* Operates at **Layer 3 / Layer 4**.

### Important Exam Points (GWLB)

* GWLB is for **security**, not web traffic.
* Centralized inspection architecture:

  * Security VPC + spoke VPCs.
* Supports inbound and outbound inspection.
* If exam mentions:

  * *“Third-party firewall appliances”* → GWLB.

---

# RDS Multi-AZ vs Read Replicas – SAA-C03 Notes

---

## Multi-AZ Deployments

* **Synchronous replication**
* Highly durable.
* Only the **primary instance** handles writes.
* Standby is not accessible.
* Automated backups taken from **standby**.
* Always spans **two AZs in one region**.
* Engine upgrades occur on primary.
* **Automatic failover** on failure.

### Purpose

* High availability
* Disaster recovery

---

## Read Replicas

* **Asynchronous replication**
* All replicas are accessible.
* Used for **read scaling**.
* No backups by default.
* Can be:

  * Same AZ
  * Cross-AZ
  * Cross-Region
* Engine upgrades are independent.
* Can be **manually promoted**.

### Purpose

* Performance
* Scalability

### Important Exam Points (RDS)

* HA requirement → **Multi-AZ**.
* Read scaling → **Read Replicas**.
* Multi-AZ ≠ scaling.
* Failover:

  * Multi-AZ → automatic
  * Read replica → manual
* Cross-region DR → Read replica.

---

## Final Exam Memory Triggers (SAA-C03)

* **Pause ASG actions → Lifecycle Hooks**
* **Replace only when app fails → ELB health checks**
* **Reduce SSL overhead → Offload**
* **Stateful apps without shared storage → Sticky sessions**
* **Firewall / IDS appliances → GWLB**
* **HA → Multi-AZ | Scaling → Read Replicas**

