# AWS S3 Security Controls

S3 is private by default

## S3 Bucket Policies

- A form of resource policy
- Like identity policies, but attached to a bucket
- Resource perspective permissions
- ALLOW/DENY same or different accounts
- ALLOW/DENY anonymous principals

## S3 Access Choices (Identity vs Bucket Policies vs ACLs)

- Identity policies: controlling different resources (prefer IAM)
- Identity policies: best for same-account access
- Bucket policies: just controlling S3
- Bucket policies: good for anonymous or cross-account access
- ACLs: **never use unless you must**

## Static Website Hosting

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

## Object Versioning & MFA Delete

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

## Quick Exam Checklist – S3 Security Controls (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **S3** | Private by default; bucket policies = resource policies. |
| **Bucket vs Identity policy** | Bucket policy: cross-account, anonymous; Identity: same-account, prefer IAM. |
| **Static website** | HTTP access; index/error doc; website endpoint; bucket policy for public read. |
| **Versioning** | Disabled → Enabled → Suspend (cannot fully disable); MFA Delete optional. |

---

## Important Exam Points – S3 Security Controls

- **S3 bucket policy** = resource policy; good for **anonymous** or **cross-account**; identity policy for same-account.
- **Versioning**: Once enabled, can only **suspend**; MFA Delete protects version deletion.
- **ACLs**: Avoid unless required; prefer bucket/identity policies.