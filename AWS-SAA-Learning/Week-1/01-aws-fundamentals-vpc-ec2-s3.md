### AWS Fundamentals

Cloud computing: On-demand delivery of IT resources over the internet with pay-as-you-go pricing.

### Region

Regions are physical locations around the world that contain multiple data centers. Each region contains at least three availability zones.

### Zone

Contains one or more data centers.

### Edge Locations

Edge locations are devices in areas outside of regions that provide quicker content access to users outside of standard regions.

### VPC

Virtual Private Cloud, a virtual network inside AWS. One default VPC per region, always associated with CIDR 172.31.0.0/16. A VPC deployed into a region is broken down into subnets, each subnet inside one availability zone.

## Elastic Compute Cloud (EC2)

EC2 is an Infrastructure as a Service (IaaS) that provides access to virtual machines called Instances. Each instance is an operating system configured in certain ways. It uses default VPC networking, is AZ resilient, and an instance fails if the AZ fails. On-demand billing per second, local on-host storage or EBS. Storage will still be used even when the Instance is in STOPPED state and there will be storage charges. In TERMINATED state, no charges are applied as the whole instance will be deleted.

## S3

S3 is a public service with unlimited data and multiple users. It is a global storage platform, regionally based/resilient (future region can be configured as per requirements). It can store data like movies, audios, photos, text, and large sets of data. It is economical and accessed via UI/CLI/API/HTTP. It has two main components: objects & buckets.

## S3 Objects

An object is like a file, made of two main components: key & value. It also has metadata, version ID, access control, and subresources.

## S3 Bucket

A bucket is created in a specific region. It can hold unlimited objects. It has no complex structure; it has a flat structure.

Bucket name has to be globally UNIQUE across all regions and across all AWS accounts. Bucket name has to be within 3-63 characters, all lowercase letters or numbers. It cannot be IP formatted (e.g., 1.1.1.1). Bucket has a 100 soft limit, 1000 hard limit per account. It has Key = Name, Value = Data.

---

## Quick Exam Checklist – Day 1 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **Regions** | Physical locations; at least 3 AZs per region; choose region for latency/compliance. |
| **Availability Zones** | Isolated data centers within a region; AZ = one or more data centers. |
| **Edge Locations** | CDN/cache points; used by CloudFront; not regions. |
| **VPC** | Virtual network; one default VPC per region (172.31.0.0/16); regional. |
| **EC2** | IaaS; VMs; AZ-resilient; on-demand per second; EBS persists when STOPPED. |
| **S3** | Object storage; global namespace; buckets regional; unlimited objects; 3–63 char bucket names, globally unique. |
| **S3 Objects** | Key + value + metadata, version ID; flat structure in bucket. |

---

## Important Exam Points – Day 1

- **Regions** are independent; most services are regional (data stays in region).
- **Default VPC** exists in every region; CIDR 172.31.0.0/16.
- **EC2**: Instance in one AZ; EBS in one AZ; storage billed when STOPPED; no charge when TERMINATED.
- **S3**: Bucket names globally unique; 100 soft / 1000 hard limit per account; no hierarchy (flat).
- **S3 durability/availability**: 99.999999999% (11 nines) durability; design for different availability/access patterns with storage classes.
