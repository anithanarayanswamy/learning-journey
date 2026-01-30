### Service Control Policies

It's a JSON policy document, it can be attached to organization, or to the group or to the members.

Service Control Policies - a feature of AWS Organizations which allow restrictions to be placed on MEMBER accounts in the form of boundaries.

- SCPs can be applied to the organization, to OUs or to individual accounts
- Member accounts can be affected, the MANAGEMENT account cannot
- SCPs DON'T GIVE permission - they just control what an account CAN and CANNOT grant via identity policies
- SCPs are account permissions boundaries
- They limit what the account (including a/c root user) can do
- They don't grant any permissions

---

## Quick Exam Checklist – Day 6 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Service Control Policies (SCPs)** | JSON policy; attach to Org, OU, or account. |
| **Scope** | Affect member accounts only; management account is NOT restricted by SCPs. |
| **Effect** | SCPs do NOT grant permissions; they set boundaries on what identity policies can allow. |
| **Use** | Restrict regions, deny certain actions, enforce org-wide guardrails. |

---

## Important Exam Points – Day 6

- **SCPs** = permission **boundaries** for accounts; they **limit** what can be granted, they **do not grant**.
- **Management account** is **not** restricted by SCPs (only member accounts are).
- SCPs apply to **organization**, **OU**, or **individual account**; inheritance from parent.
- "Allow list" vs "deny list" SCPs: deny list is common (e.g. deny all except approved regions).
- SCPs work with **AWS Organizations**; must have Organizations enabled.