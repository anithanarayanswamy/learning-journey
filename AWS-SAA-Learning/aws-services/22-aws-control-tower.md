# AWS Control Tower

- Quick & easy setup of multiple account environment
- Orchestrates other AWS services to provide this functionality
- Organization, IAM Identity Center, CloudFormation, Config & more
- Landing zone - multi-account environment
- SSO/ID Federation, centralized logging & auditing
- Guardrails - detect/mandate rules/standardize new account creation
- Dashboard - single page oversight of the entire environment

## AWS Control Tower - Landing Zone

- **Well Architected multi-account environment - Home Region**
- Built with AWS Organizations, AWS Config, CloudFormation
- **Security OU** - Log Archive & Audit Accounts (CloudTrail & Config Logs)
- **Sandbox OU** - Test/less rigid security
- You can create other OU's and Accounts
- **IAM Identity Center (AWS SSO)** - SSO, multiple-accounts, ID Federation
- **Monitoring and Notifications** - CloudWatch and SNS
- **End User account provisioning** via Service Catalog

## AWS Control Tower - Guardrails

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

## AWS Control Tower - Account Factory

- **Automated Account Provisioning**
- Cloud admins or end users (with appropriate permissions) can provision accounts
- **Guardrails - automatically added** to new accounts
- **Account admin given to a named user** (IAM Identity Center)
- **Account & network standard configuration**
- Accounts can be **closed or repurposed**
- Can be **fully integrated with a business's SDLC**

---

## Quick Exam Checklist – Control Tower (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Control Tower** | Multi-account landing zone; Organizations + Config + SSO; guardrails (preventive/detective). |
| **Landing Zone** | Log Archive + Audit accounts; Security OU; IAM Identity Center. |
| **Guardrails** | Preventive (SCPs) vs Detective (Config rules); mandatory / strongly recommended / elective. |
| **Account Factory** | Automated account provisioning; guardrails auto-applied. |

---

## Important Exam Points – Control Tower

- **Control Tower** = landing zone + guardrails; **preventive** = SCPs; **detective** = Config rules.