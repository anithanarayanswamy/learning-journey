## DAY 3

### CloudWatch Basics: CORE PRODUCT

CloudWatch collects & manages operational data (by environments, logging data, etc.).

CloudWatch has three main components:

- **Metrics**: Collects the metrics of AWS products, apps, On-premises (data related like CPU usage, memory usage, disk space usage)
- **CloudWatch Logs**: AWS products, apps, On-premises
- **CloudWatch Events**: AWS products, apps, On-premises (SNS)

## DAY 4

- **High Availability**: Minimize any outage (Does not mean services are always available)
- **Fault-Tolerance**: Operate through faults (Does not mean there are no faults)
- **Disaster Recovery**: Used when the above two don't work

### Route53: Register Domains

- Host zones managed nameservers
- Global service single database
- Globally resilient
- Allows you to register domains

### IAM

IAM is a set of security statements in AWS policies. It grants & denies access to AWS products & features to any identities which use policies.

- IAM policies use JSON format
- IAM policies consist of STATEMENTS
- The policies' first part is Statement ID (SID), then Effect, Action, Resources are the main parts
- The FIRST priority is Explicit "DENY"
- The SECOND priority is Explicit "ALLOW"
- The THIRD priority is DEFAULT Implicit "DENY"

#### MANAGED POLICIES

Managed policies are created as own objects and created for two main reasons: for reusable (normal, default rights) and to allow management overhead.

## DAY 5

### IAM USERS

IAM users are an identity used for anything requiring long-term AWS access, for example: Humans, Applications, or Service accounts.

**Principals**: Represent an entity, like human, applications, or service accounts.

In order to access IAM, the principals must AUTHENTICATE & be AUTHORIZED to make requests to IAM.

If the principal is a user, then it can authenticate using username & password. If it's an app or service account, it can use an access key to identify itself and to access IAM. Once the principal becomes an Authenticated Identity, it also requires AUTHORIZATION to make any actions in any AWS products.

**Amazon Resource Name (ARN)**

Uniquely identifies resources within AWS accounts. ARNs are a collection of fields split by colon.

```
arn:partition:service:region:account-id:resource-id
```

- 5000 IAM users per account
- IAM user can be a member of 10 groups
- This has systems design impact

**DEMO lesson**: Created an IAM user and experimented with assigning permissions on two S3 buckets via inline policies and managed policies.

### IAM Groups

IAM Groups are containers for users. You cannot login to groups because they do not have any credentials. No built-in groups exist natively in IAM. You can add IAM users to groups and add permissions to Groups. You can add IAM users to groups and add permissions to Groups. There are no nested groups. Group has a limit of 300 groups (soft limit).

**Demo lesson**: Created a group called developer, added user, added permissions.

### IAM ROLES

A role is a type of identity. A role is generally used by an unknown number or multiple principal users.

**Candidates for IAM ROLES**

- Multiple AWS users inside AWS account
- Could be humans, apps, or services inside or outside AWS account
- More than 5000 principals
- Generally used on a temporary basis, something becomes a role for a short period & then stops
- A role is something that represents access inside an AWS account

IAM roles have two policies: trust policy and permission policy.

#### Identity Federation

Giving permission to external identities & allowing them to assume the role is called Identity Federation.

#### Service Linked Roles

IAM role linked to a specific AWS service, predefined by a service, providing permissions that services need on your behalf. The service might create or delete the role, or allow you to during setup or within IAM. You cannot delete the role until it is no longer required.

### AWS ORGANIZATIONS

Using Organizations, you can create accounts and allocate resources, group accounts to organize your workflows, apply policies for governance, and simplify billing by using a single payment method for all of your accounts. We can use AWS Organizations to:

- Create or add existing accounts
- Add group accounts
- Apply policies to accounts
- Enable AWS services
