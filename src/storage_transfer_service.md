# Storage Transfer Service: ACE Exam Study Guide (2026)

## 1. Overview and Use Cases

Storage Transfer Service (STS) is a fully managed service for moving large volumes of data into Cloud Storage (GCS) from other cloud providers, on-premises locations, or within GCS itself.

- Source Support: AWS S3, Azure Blob Storage, HTTP/HTTPS locations, On-premises (using agents), and Google Cloud Storage (GCS).
- Destination Support: Always Google Cloud Storage (GCS) buckets.
- Key Benefit: Managed scaling, scheduling, and error handling without requiring you to manage VMs or scripts.

## 2. Core Architecture Components

- Transfer Job: A configuration that defines the source, destination, filters, and schedule.
- Transfer Agents (On-premises only): Lightweight software installed on your local hardware to facilitate data transfer to GCP.
- STS Service Account: A Google-managed service account that performs the transfer. It requires permissions (like `storage.admin`) on both source and destination buckets.
- Manifest Files: A CSV file that lists specific objects to be transferred, allowing for granular control.

## 3. The Migration Lifecycle

1. Source Setup: Grant the STS Service Account permission to read from the source (e.g., AWS S3 bucket) and write to the destination (GCS bucket).
2. Create Transfer Job: Define the source (AWS, Azure, GCS, etc.) and the destination bucket.
3. Configure Options:
   - Scheduling: One-time vs. Recurring (daily/weekly).
   - Filtering: Include or exclude objects based on prefix or suffix.
   - Overwrite/Delete: Choose whether to overwrite existing files or delete source files after transfer (use with caution).
4. Monitoring: Use the Cloud Console or Cloud Monitoring to track the progress and status of the transfer job.

## 4. STS vs. Other Transfer Tools (High Frequency Exam Topic)

- Storage Transfer Service (STS): Best for cloud-to-cloud (S3 to GCS), scheduled/recurring transfers, or massive on-premises data (1TB+ with good bandwidth).
- Transfer Appliance: Best for massive on-premises data (60TB+) where bandwidth is too slow for online transfer (offline "truck-based" transfer).
- `gcloud storage` (formerly `gsutil`): Best for small, ad-hoc transfers (< 1TB) or developer-driven scripts.
- Database Migration Service (DMS): Use for databases, NOT for unstructured file data.

## 5. Security and Compliance

- Identity Federation: In 2026, the exam emphasizes using OIDC (OpenID Connect) for AWS/Azure transfers instead of long-term Access/Secret keys.
- Data Integrity: STS automatically performs checksum validation (CRC32C) to ensure data is not corrupted during transit.
- Encryption: Data is encrypted in transit using HTTPS/TLS and at rest in GCS using default or Customer-Managed Encryption Keys (CMEK).

## 6. Key Exam Tips and Gotchas

- Incremental Transfers: STS only copies new or changed objects (based on checksums and file size) to save time and cost.
- Event-Driven Transfers: STS can be triggered by events (e.g., a new file appearing in an S3 bucket), reducing latency for real-time workflows.
- Permissions: If a transfer fails, the first check is ALWAYS the STS Service Account's permissions on the source and destination.
- Deletion Policy: You can configure STS to delete the source files after a successful transfer (useful for moving logs to long-term storage).
- Bandwidth Throttling: For on-premises transfers, you can set limits to avoid saturating your local internet connection.

## 7. 2026 Updates

- Gemini Integration: Use Gemini in the Cloud Console to assist in troubleshooting failed transfer jobs or generating `gcloud transfer` commands based on natural language.
- Event-Driven Transfers: Now a standard feature for real-time synchronization between cloud providers.
- OIDC Adoption: Moving away from static credentials for cross-cloud transfers.
