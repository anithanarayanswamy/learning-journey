# AWS S3 Overview

S3 is a public service with unlimited data and multiple users. It is a global storage platform, regionally based/resilient (future region can be configured as per requirements). It can store data like movies, audios, photos, text, and large sets of data. It is economical and accessed via UI/CLI/API/HTTP. It has two main components: objects & buckets.

## S3 Bucket

A bucket is created in a specific region. It can hold unlimited objects. It has no complex structure; it has a flat structure.

Bucket name has to be globally UNIQUE across all regions and across all AWS accounts. Bucket name has to be within 3-63 characters, all lowercase letters or numbers. It cannot be IP formatted (e.g., 1.1.1.1). Bucket has a 100 soft limit, 1000 hard limit per account. It has Key = Name, Value = Data.

---

## Quick Exam Checklist – AWS S3 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **S3** | Object storage; global namespace; buckets regional; unlimited objects; 3–63 char bucket names, globally unique. |
| **S3 Objects** | Key + value + metadata, version ID; flat structure in bucket. |

---

## Important Exam Points – AWS S3

- **S3**: Bucket names globally unique; 100 soft / 1000 hard limit per account; no hierarchy (flat).
- **S3 durability/availability**: 99.999999999% (11 nines) durability; design for different availability/access patterns with storage classes.