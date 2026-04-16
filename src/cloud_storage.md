# Cloud Storage: ACE Exam Study Guide (2026)

![Cloud Storage](images/cloud_storage.png)

_Image source: Google Cloud Documentation_

## 1. Cloud Storage Overview

Cloud Storage is Google Cloud's object storage service for storing unstructured data such as files, images, backups, and static website assets.

- **Characteristics:**
  - **Global Namespace:** Bucket names must be globally unique across all of Google Cloud.
  - **Durability:** Designed for 99.999999999% (11 nines) annual durability.
  - **Consistency:** Provides strong global consistency for all operations (read-after-write, list-after-write).

## 2. Bucket Locations

- **Regional:** Stored in a single region. Lowest latency for compute in the same region.
- **Dual-Region:** Stored in two specific regions for high availability (99.99%) and disaster recovery.
- **Multi-Region:** Stored across large geographic areas (e.g., US, EU) for global content distribution.

## 3. Storage Classes

| Storage Class | Use Case                       | Min Duration |
| ------------- | ------------------------------ | ------------ |
| **Standard**  | Hot data, frequent access      | None         |
| **Nearline**  | Access ~once per month         | 30 days      |
| **Coldline**  | Access ~once per quarter       | 90 days      |
| **Archive**   | Rare access, long-term storage | 365 days     |

> **Autoclass (2026 Standard):** Automatically moves objects to colder storage classes based on access patterns to optimize costs without manual intervention.

## 4. Access Control

- **IAM (Recommended):** Controls access at the bucket or project level.
- **Uniform Bucket-Level Access (UBLA):** Recommended for most use cases. It **disables ACLs entirely** and relies solely on IAM for better security management.
- **ACLs (Legacy):** Provides object-level permissions but is harder to manage at scale.
- **Signed URLs:** Provide temporary access to a specific object without requiring a Google account for the recipient. Perfect for sharing private content via a link.

## 5. Object Versioning and Lifecycle Management

- **Object Versioning:** Keeps old versions of objects to protect against accidental deletions or overwrites.
- **Lifecycle Rules:** Automate actions such as moving objects to a cheaper storage class or deleting them.
  - **Common Conditions:** `Age` (days), `CreatedBefore` (date), `IsLive` (true/false), `MatchesStorageClass`.
- **Soft Delete (2026 Standard):** A bucket-level setting that allows you to recover deleted objects for a configurable retention period (default 7 days) even after they are deleted.

## 6. Retention Policies and Holds

- **Retention Policy:** Ensures objects are not deleted or overwritten for a specific duration.
- **Bucket Lock:** Once a retention policy is locked, it cannot be removed or shortened.
- **Object Holds:** Prevents deletion of specific objects for legal or event-based reasons.

## 7. Encryption

- **Google-Managed Keys:** The _default_ encryption for all data at rest.
- **Customer-Managed Encryption Keys (CMEK):** Keys stored in Cloud KMS. The KMS key must be in the same region as the bucket.
- **Customer-Supplied Encryption Keys (CSEK):** You provide the raw key with each request.

> Data is always encrypted at rest in Cloud Storage.

## 8. Data Migration Tools

- **`gcloud storage`:** The modern, multi-threaded CLI for interacting with Cloud Storage (replaces `gsutil`).
- **`gsutil`:** Legacy tool, still functional but slower than `gcloud storage`.
- **Storage Transfer Service:** Move data from AWS S3, Azure, or other GCS buckets.
- **Transfer Appliance:** A physical device for massive data migration (petabytes).

## 9. Performance and Triggers

- **Resumable Uploads:** Allows you to resume an upload after a communication failure. Recommended for files > 10MB or unstable networks.
- **Parallel Composite Uploads:** The `gcloud storage` CLI automatically splits large files into chunks, uploads them in parallel, and "composes" them into one final object. This significantly increases speed for large files.
- **Combined Approach:** The modern `gcloud storage cp` command combines both—it uploads chunks in parallel, and each individual chunk upload is resumable, ensuring both high speed and reliability for massive files.
- **Requester Pays:** The person accessing the data pays the egress costs instead of the bucket owner.
- **Cloud Storage Triggers:** GCS can trigger **Cloud Functions** or **Cloud Run** jobs immediately after an object is created, deleted, or archived (via Pub/Sub notifications).

## 10. Common ACE Exam Scenarios

- **Scenario**: Upload a 100GB file over an unstable connection? → Use **Resumable Uploads**.
- **Scenario**: Speed up the upload of a 1TB file? → Use **Parallel Composite Uploads** (via `gcloud storage`).
- **Scenario**: Process a file as soon as it's uploaded? → Use **Cloud Storage Triggers** (GCS → Pub/Sub → Cloud Functions).
- **Scenario**: Automatically reduce costs for old data? → Use **Lifecycle Rules** or **Autoclass**.
- **Scenario**: Avoid ACL complexity? → Enable **Uniform Bucket-Level Access**.
- **Scenario**: High Availability for a single region? → Use **Dual-Region**.
- **Scenario**: Protect against "fat-finger" accidental deletion? → Enable **Soft Delete** or **Object Versioning**.
- **Scenario**: Give a non-GCP user temporary access to a file? → Use a **Signed URL**.

## 11. External Links

- [Cloud Storage - The Cloud Girl](https://www.thecloudgirl.dev/storage2/cloud-storage)
- [Which Storage Should I Use - The Cloud Girl](https://www.thecloudgirl.dev/storage2/which-storage-should-i-use)
- [What are different storage types - The Cloud Girl](https://www.thecloudgirl.dev/storage2/types-of-storage)
