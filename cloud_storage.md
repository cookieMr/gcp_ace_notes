# Cloud Storage: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Cloud Storage Overview

Cloud Storage is Google Cloud's object storage service for storing unstructured data such as files, images, backups, and static website assets.

- **Characteristics:**
  - **Global Namespace:** Bucket names must be globally unique across all of Google Cloud.
  - **Durability:** Designed for 99.999999999% (11 nines) annual durability.
  - **Consistency:** Provides strong global consistency for all operations (read-after-write, list-after-write).
  - **Gemini Integration:** Gemini in the Cloud Console can help generate lifecycle rules, explain bucket configurations, and suggest storage optimizations.

## 2. Bucket Locations

- **Regional:** Stored in a single region. Lowest latency for compute in the same region.
- **Dual-Region:** Stored in two specific regions for high availability and disaster recovery.
- **Multi-Region:** Stored across large geographic areas (e.g., US, EU) for global content distribution.

## 3. Storage Classes

| Storage Class | Use Case | Min Duration |
| ------------- | -------- | ------------ |
| **Standard** | Hot data, frequent access | None |
| **Nearline** | Access ~once per month | 30 days |
| **Coldline** | Access ~once per quarter | 90 days |
| **Archive** | Rare access, long-term storage | 365 days |

- **Autoclass (2026 Standard):** Automatically moves objects to colder storage classes based on access patterns to optimize costs without manual intervention.

## 4. Access Control

- **IAM (Recommended):** Controls access at the bucket or project level.
- **Uniform Bucket-Level Access:** Recommended for most use cases; it disables ACLs and relies solely on IAM for better security management.
- **ACLs (Legacy):** Provides object-level permissions but is harder to manage at scale.
- **Signed URLs:** Provide temporary access to a specific object without requiring a Google account for the recipient.

## 5. Object Versioning and Lifecycle Management

- **Object Versioning:** Keeps old versions of objects to protect against accidental deletions or overwrites.
- **Lifecycle Rules:** Automate actions such as moving objects to a cheaper storage class or deleting them after a certain number of days.
- **Soft Delete (2026 Standard):** A bucket-level setting that allows you to recover deleted objects for a configurable retention period (default 7 days) even after they are deleted.

## 6. Retention Policies and Holds

- **Retention Policy:** Ensures objects are not deleted or overwritten for a specific duration.
- **Bucket Lock:** Once a retention policy is locked, it cannot be removed or shortened.
- **Object Holds:** Prevents deletion of specific objects for legal or event-based reasons.

## 7. Encryption

- **Google-Managed Keys:** The default encryption for all data at rest.
- **Customer-Managed Encryption Keys (CMEK):** Keys stored in Cloud KMS. For CMEK, the KMS key must be in the same region as the bucket.
- **Customer-Supplied Encryption Keys (CSEK):** You provide the raw key with each request.

## 8. Data Migration Tools

- **gcloud storage:** The modern, multi-threaded CLI for interacting with Cloud Storage (replaces `gsutil`).
- **Storage Transfer Service:** Move data from AWS S3, Azure, or other GCS buckets.
- **Transfer Appliance:** A physical device for massive data migration (petabytes).

## 9. Performance and Networking

- **Parallel Composite Uploads:** Speeds up large object uploads.
- **Requester Pays:** The person accessing the data pays the egress costs instead of the bucket owner.
- **Static Website Hosting:** Buckets can serve static content. Requires public access and an `index.html` file.

## 10. Exam Tips

- **Lifecycle Rules:** Often the answer for "How to automatically reduce costs for old data."
- **Standard Storage:** Best for frequently accessed data or high-performance computing.
- **Archive Storage:** Best for regulatory compliance where data is rarely accessed.
- **Signed URL:** The correct choice for giving a non-GCP user temporary access to a private file.
- **gcloud storage cp:** Use this command for basic file transfers in the CLI.

[Back to README](README.md)
