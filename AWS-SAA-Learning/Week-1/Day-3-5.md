DAY 3
### Cloud Watch Basics: CORE PRODUCT
It collects & manages operational data (by environments, logging data, etc..)
cloud watch have three main components:
Metrics: Collects the metrics of AWS products, apps, On-premises(data related like CPU usage, memeory usage, disk space usage)
Cloud Watch Logs: AWS products, apps, On-premises
Cloud Watch events: AWS products, apps, On-premises (SNS)

DAY 4 

High Availablity-minise any outage (Does not means always services are available)
Fault-Tolerance-operate through faults(Does not means there are no faults)
Diaster Recover-Used when tabove these don't work.

### Route53: Register Domains
Host zones managed nameservers
Gobal service single database
globally resilliant
allow you to register domains.

### IAM
 It is a set of security statements in AWS policies, it grants & denies access to AWS products & features to any identies which uses policies.
 IAM policies uses JSON format
 IAM policies consist with STATEMENTS
 the policies first part is Statement ID(SID), then Effect, Action, Resources are the main parts.
 The FIRST priority is Explicit "DENY"
 The SECOND priority is Explicit "ALLOW"
 The THIRD priority is DEFAULT Implicit "DENY"

 #### MANAGED POLICIES:
 Managed polocies are created as own objects and created for two main reasons for resuable(normal, default rights) and to allow management over head.

 DAY 5

 ### IAM USERS

 IAM users are an identity used for anything requiring longterm AWS access example Humans, Applications or Service accounts.

 Pricipals: reperesnt entity, like human, applications or service accounts.
 In order to access the IAM the pricipals must AUTHENTICATE & AUTHORISED to make request the IAM.
 if the pricipal is a user then it can authenticate using username & password, or if its a app or service accounts it can use a access key to identiy itself and to access the IAM. Once the principal becomes
 Authenticated Identity, also then it requires AUTHORISATION to make any actions in any AWS products.
 


