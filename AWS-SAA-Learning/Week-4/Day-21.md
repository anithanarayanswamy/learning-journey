## Simple Queue Service (SQS)

SQS is a **fully managed, highly available queue service**.

### Core concepts

* Public AWS service
* Queue types:

  * **Standard**
  * **FIFO**

### Message handling

* Message size: **≤ 256 KB**
* Large data stored externally (e.g. S3)

### Visibility Timeout

* Messages become **hidden** after being read
* Prevents multiple consumers processing same message

### Message lifecycle

* Message is:

  * Processed and **deleted**
  * OR becomes visible again (retry)

### Dead-Letter Queues (DLQ)

* Used to capture **failed or poison messages**

### Scaling

* ASGs and Lambda can scale based on **queue depth**

---