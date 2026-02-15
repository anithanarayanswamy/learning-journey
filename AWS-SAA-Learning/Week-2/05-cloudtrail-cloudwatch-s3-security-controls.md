#### CloudWatch Logs

Public service - usable from AWS or on-premises. Store, monitor and access logging data.

- AWS integrations - EC2, VPC flow logs, Lambda, CloudTrail, Route53 and more...
- Can generate metrics based on logs - metric filter
- It's a regional service, starting point of logging sources which include AWS services or products, mobile apps, server-based apps, external apps or services

#### CloudTrail

CloudTrail is a record of activity in AWS account. It can be actions taken by user or account.

- Logs API calls/activities as a CloudTrail Event
- 90 days stored by default in event history
- Enabled by default, no cost for 90 day history
- To customize the service, create 1 or more trails
- Management events and data events are all captured. Data events not enabled by default
- It can be configured to store data indefinitely in S3 or CloudWatch Logs
- It's very often used to diagnose security or performance issues, or to provide quality account level traceability
- It's a regional service, it can also log global services
- By default, CloudTrail logs are stored in S3 buckets, only charged for storage in S3. It can also put data in CloudWatch Logs
- It can now be created by management account
- Management events only by default
- **IAM, STS, CloudFront => global service events** [they log their data as global service events, which gets logged to us-east-1, trail will need to be enabled to capture that data]
- Not real-time - there is a delay

#### AWS Control Tower

- Quick & easy setup of multiple account environment
- Orchestrates other AWS services to provide this functionality
- Organization, IAM Identity Center, CloudFormation, Config & more
- Landing zone - multi-account environment
- SSO/ID Federation, centralized logging & auditing
- Guardrails - detect/mandate rules/standardize new account creation
- Dashboard - single page oversight of the entire environment

##### AWS Control Tower - Landing Zone

- **Well Architected multi-account environment - Home Region**
- Built with AWS Organizations, AWS Config, CloudFormation
- **Security OU** - Log Archive & Audit Accounts (CloudTrail & Config Logs)
- **Sandbox OU** - Test/less rigid security
- You can create other OU's and Accounts
- **IAM Identity Center (AWS SSO)** - SSO, multiple-accounts, ID Federation
- **Monitoring and Notifications** - CloudWatch and SNS
- **End User account provisioning** via Service Catalog

##### AWS Control Tower - Guardrails

- **Guardrails are rules** - multi-account governance
- They can be **Mandatory, Strongly Recommended or Elective**

**Preventive Guardrails:**

- **Stop you doing things** (AWS ORG SCP - Service Control Policies)
- Can be **enforced or not enabled**
- Examples include: **allow or deny regions or disallow bucket policy changes**

**Detective Guardrails:**

- Perform **compliance checks** (AWS CONFIG Rules)
- Their status can be **clear, in violation or not enabled**
- Examples include: **detect CloudTrail enabled or EC2 Public IPv4**

##### AWS Control Tower - Account Factory

- **Automated Account Provisioning**
- Cloud admins or end users (with appropriate permissions) can provision accounts
- **Guardrails - automatically added** to new accounts
- **Account admin given to a named user** (IAM Identity Center)
- **Account & network standard configuration**
- Accounts can be **closed or repurposed**
- Can be **fully integrated with a business's SDLC**

## S3
S3 is private by default

### S3 Bucket Policies

- A form of resource policy
- Like identity policies, but attached to a bucket
- Resource perspective permissions
- ALLOW/DENY same or different accounts
- ALLOW/DENY anonymous principals

### S3 Access Choices (Identity vs Bucket Policies vs ACLs)

- Identity policies: controlling different resources (prefer IAM)
- Identity policies: best for same-account access
- Bucket policies: just controlling S3
- Bucket policies: good for anonymous or cross-account access
- ACLs: **never use unless you must**

### Static Website Hosting

- Normal access is via AWS APIs
- Feature allows access via HTTP (e.g., blogs)
- Index and Error documents are set
- A Website Endpoint is created
- Custom Domain via Route53 (bucket name matters)

Demo lesson for S3 static page
 Created an S3 bucket, enabled static website hosting, and uploaded `index.html` and `error.html` (plus supporting assets). Added the bucket policy below to allow anonymous read access for the static site.
 {
    "Version":"2012-10-17",
    "Statement":[
      {
        "Sid":"PublicRead",
        "Effect":"Allow",
        "Principal": "*",
        "Action":["s3:GetObject"],
        "Resource":["arn:aws:s3:::examplebucket/*"]
      }
    ]
  }

#### Object Versioning & MFA Delete

- Controlled at the bucket level
- Starts in a **Disabled** state; you can enable it
- Once enabled, you cannot fully disable it; you can only **Suspend** it
- A suspended bucket can be re-enabled
- Versioning lets you store multiple versions of objects in a bucket
- Any operation that would modify an object generates a new version
- The latest version (current version) is the one returned by default
- **MFA Delete** is enabled in the versioning configuration
- MFA is required to change the bucket versioning state
- MFA is required to delete object versions
- Serial number (MFA) + code must be passed with API calls

---

## Quick Exam Checklist – Day 7 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **CloudWatch Logs** | Store/monitor logs; regional; metric filters; AWS and on-prem. |
| **CloudTrail** | API activity; 90-day event history by default; trails to S3/CloudWatch; management + data events. |
| **CloudTrail** | Data events not enabled by default; global services (IAM, STS, CloudFront) log to us-east-1. |
| **Control Tower** | Multi-account landing zone; Organizations + Config + SSO; guardrails (preventive/detective). |
| **Landing Zone** | Log Archive + Audit accounts; Security OU; IAM Identity Center. |
| **Guardrails** | Preventive (SCPs) vs Detective (Config rules); mandatory / strongly recommended / elective. |
| **Account Factory** | Automated account provisioning; guardrails auto-applied. |
| **S3** | Private by default; bucket policies = resource policies. |
| **Bucket vs Identity policy** | Bucket policy: cross-account, anonymous; Identity: same-account, prefer IAM. |
| **Static website** | HTTP access; index/error doc; website endpoint; bucket policy for public read. |
| **Versioning** | Disabled → Enabled → Suspend (cannot fully disable); MFA Delete optional. |

---

## Important Exam Points – Day 7

- **CloudTrail**: 90 days in event history; **trails** for long-term (S3/CloudWatch); **data events** off by default; global services → us-east-1.
- **Control Tower** = landing zone + guardrails; **preventive** = SCPs; **detective** = Config rules.
- **S3 bucket policy** = resource policy; good for **anonymous** or **cross-account**; identity policy for same-account.
- **Versioning**: Once enabled, can only **suspend**; MFA Delete protects version deletion.
- **ACLs**: Avoid unless required; prefer bucket/identity policies.

