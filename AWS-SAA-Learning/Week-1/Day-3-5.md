DAY 3
### Cloud Watch Basics: CORE PRODUCT
It collects & manages operational data (by environments, logging data, etc..)
cloud watch have three main components:
Metrics: Collects the metrics of AWS products, apps, On-premises(data related like CPU usage, memory usage, disk space usage)
Cloud Watch Logs: AWS products, apps, On-premises
Cloud Watch events: AWS products, apps, On-premises (SNS)

DAY 4 

High Availability-minimize any outage (Does not means always services are available)
Fault-Tolerance-operate through faults(Does not means there are no faults)
Disaster Recover-Used when the above these don't work.

### Route53: Register Domains
Host zones managed nameservers
Global service single database
globally resilient
allow you to register domains.

### IAM
 It is a set of security statements in AWS policies, it grants & denies access to AWS products & features to any identities which uses policies.
 IAM policies uses JSON format
 IAM policies consist with STATEMENTS
 the policies first part is Statement ID(SID), then Effect, Action, Resources are the main parts.
 The FIRST priority is Explicit "DENY"
 The SECOND priority is Explicit "ALLOW"
 The THIRD priority is DEFAULT Implicit "DENY"

 #### MANAGED POLICIES:
 Managed policies are created as own objects and created for two main reasons for reusable(normal, default rights) and to allow management over head.

 DAY 5

 ### IAM USERS

 IAM users are an identity used for anything requiring longterm AWS access example Humans, Applications or Service accounts.

 Principals: represent entity, like human, applications or service accounts.
 In order to access the IAM the principals must AUTHENTICATE & AUTHORIZED to make request the IAM.
 if the principal is a user then it can authenticate using username & password, or if its a app or service accounts it can use a access key to identify itself and to access the IAM. Once the principal becomes
 Authenticated Identity, also then it requires AUTHORIZATION to make any actions in any AWS products.

 Amazon Resources Name(ARN)
  Uniquely identify resources within aws accounts, arn are a collection of fields split by colon.
  arn:partition:service:region:account-id:resource-id

  *5000 IAM users per account
  *IAM user can be a member of 10 groups
  *this has systems design impact.

DEMO lesson: created an IAM user, and experiment with assigning permissions on two S3 buckets via inline policies and managed policies.

### IAM Groups
 IAM Groups are container for users. You can not login to groups because it does not have any credentials, no built in groups in IAM does not exist natively. You can add IAM users to groups, and add permissions to Groups. You can add IAM users to groups, and add permissions to Groups. There are no nested group. Group has limit of 300 groups soft limit. 

 Demo lesson: created a group called developer, added user, added permissions.

### IAM ROLES
 Role is a type of identity, role is generally used by unknown number or multiple principal users. 
 
 Candidates for IAM ROLES
 Multiple aws users inside aws a/c.
 Could be humans, app or services inside or outside aws a/c.
 more than 5000 principal
 generally used on temporary basis, something becomes a role for short period & then stops.
 role is something that represent access inside aws a/c.
 IAM roles has two policies trust policy, permission policy.

 #### ID Federation: 
 Giving permission to external identities & allowing them to assume the role called ID Federation. 

 #### Service linked roles
  IAM role linked to a specific AWS service, predefined by a service, providing permission that services on your behalf, service might create delete the role, or allow you to during set up or with in IAM, you cannot delete the role until it is no longer required.

### AWS ORGANIZATION
 Using Organizations, you can create accounts and allocate resources, group accounts to organize your workflows, apply policies for governance, and simplify billing by using a single payment method for all of your accounts. We can use AWS Organizations to create or Add existing accounts, add Group accounts, Apply policies to accounts, enable AWS services

