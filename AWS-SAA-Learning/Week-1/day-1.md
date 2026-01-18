### AWS Fundamentals
Cloud computing : On demand delivery of IT resources over the internet with pay as you go pricing.

### Region:
 are physical locations around the world that contain multiple data centers, each regions contains at least three availability zones.

### Zone:  
contains one or more data centers

### Edge Locations: 
are devices in areas outside of regions. That provides quicker content access to users outside of standard regions. 

### VPC: 
Virtual Private Cloud, a virtual network inside AWS. One default VPC per one region, is always associated with CIDR 172.31.0.0/16. VPC is deployed into a region is broken down into subnets, each subnets inside in one availability zone.

## Elastic Compute Cloud EC2: 
is a infrastructure as a service it provides access to virtual machines called Instances each instances is a operating system configured with certain ways. It uses default VPC networking, AZ resilient, instance fails if AZ fails. On-demand billing per second, Local on host storage or EBS. Storage will still be used even the Instance is in STOPPED state and there will be storage charges. In TERMINATED state no charges are applied as the whole instance will be deleted.

## S3
 It is a public service, with unlimited data and multiple users, it is a global storage platform regional based/resilient (future region can be configured as per requirements), can have storage data like movies, audios, photos, text, large set of data. It is economical and accessed via UI/CLI/API/HTTP, it has 2 main things objects & buckets

## S3 Objects
 it is like a file, made of 2 main components key & value and it also have meta data, version id, access control, subresourses. 

## S3 Bucket
Bucket is created in a specific region, it can hold unlimited objects, it has no complex structure, it has flat structure. 
Bucket name has to globally UNIQUE across all regions, across all AWS accounts. Bucket name has to be with in 3-63 characters, all lowercase letter or a number, It can not be ip formatted e.g. 1.1.1.1. Bucket has 100 soft limit, 1000 hard limit per account. It has Key =Name, Value = Data