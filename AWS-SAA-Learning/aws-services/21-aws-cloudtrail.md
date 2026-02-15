# AWS CloudTrail

CloudTrail is a record of activity in AWS account. It can be actions taken by user or account.

- Logs API calls/activities as a CloudTrail Event
- 90 days stored by default in event history
- Enabled by default, no cost for 90 day history
- To customize the service, create 1 or more trails
- Management events and data events are all captured. Data events not enabled by default
- It can be configured to store data indefinitely in S3 or CloudWatch Logs
- It's very often used to diagnose security or performance issues, or to provide quality account level traceability
- It's a regional service, it can also log global services
- By default, CloudTrail logs are stored in S3 buckets, only charged for storage in S3. It can also put data in CloudWatch Logs
- It can now be created by management account
- Management events only by default
- **IAM, STS, CloudFront => global service events** [they log their data as global service events, which gets logged to us-east-1, trail will need to be enabled to capture that data]
- Not real-time - there is a delay

---

## Quick Exam Checklist – CloudTrail (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **CloudTrail** | API activity; 90-day event history by default; trails to S3/CloudWatch; management + data events. |
| **CloudTrail** | Data events not enabled by default; global services (IAM, STS, CloudFront) log to us-east-1. |

---

## Important Exam Points – CloudTrail

- **CloudTrail**: 90 days in event history by default; **trails** for long-term (S3/CloudWatch); **data events** off by default; global services → us-east-1.