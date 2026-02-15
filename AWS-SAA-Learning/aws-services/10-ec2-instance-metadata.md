# EC2 Instance Metadata

**Instance Metadata Service (IMDS)** provides **information about the instance** to applications **running on that instance**.

- **Access**
  - Available at the **special link-local address** `http://169.254.169.254`.
  - Only accessible **from inside the instance**.

- **Information available**
  - **Environment**: instance ID, AMI ID, instance type, region/AZ.
  - **Networking**: IP addresses, security groups, etc.
  - **Authentication**: IAM **role credentials** for the instance profile.
  - **User data**: bootstrap script provided at instance launch.

- **Security**
  - Traditionally **not authenticated or encrypted** (plain HTTP).
  - AWS introduced **IMDSv2** (recommended) which uses **session tokens** to mitigate SSRF-style attacks.

- **Important exam points (Instance Metadata)**
  - Accessed via **`http://169.254.169.254`** **from within the instance only**.
  - Used by applications and the AWS SDK to **retrieve IAM role credentials**.
  - **Never expose** the metadata endpoint to the internet.

---

## Quick Exam Checklist – Instance Metadata (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Instance Metadata** | 169.254.169.254; IAM creds, user-data; IMDSv2 recommended. |

---

## Important Exam Points – Instance Metadata

- **Instance Metadata**: Accessed via **`http://169.254.169.254`** **from within the instance only**.
- Used by applications and the AWS SDK to **retrieve IAM role credentials**.
- **Never expose** the metadata endpoint to the internet.