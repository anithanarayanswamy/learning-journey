### Service Control Policies

It's a JSON policy document, it can be attached to organization, or to the group or to the members.

Service Control Policies - a feature of AWS Organizations which allow restrictions to be placed on MEMBER accounts in the form of boundaries.

- SCPs can be applied to the organization, to OUs or to individual accounts
- Member accounts can be affected, the MANAGEMENT account cannot
- SCPs DON'T GIVE permission - they just control what an account CAN and CANNOT grant via identity policies
- SCPs are account permissions boundaries
- They limit what the account (including a/c root user) can do
- They don't grant any permissions