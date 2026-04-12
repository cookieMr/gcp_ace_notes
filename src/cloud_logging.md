# Cloud Logging: ACE Exam Study Guide (2026)

![Cloud Logging](images/cloud_logging.png)

_Image source: Google Cloud Documentation_

## 1. Cloud Logging Overview

Cloud Logging is a fully managed service that allows you to store, search, analyze, and alert on log data and events from Google Cloud.

### Key Characteristics

- **Unified:** Collects logs from all GCP services (Compute Engine, GKE, Cloud Run, etc.) and even multi-cloud/on-premises sources.
- **Integrated:** Works seamlessly with Cloud Monitoring and Cloud Error Reporting.
- **Retention:** Logs are kept for a specific period (standard is 30 days) and then automatically deleted.

## 2. Log Buckets and Log Analytics

Logs are stored in **Log Buckets** (not Cloud Storage buckets).

- **Default Buckets:**
  - **\_Default:** For all standard logs (e.g., App Engine, Cloud Functions).
  - **\_Required:** For essential logs like Audit Logs (cannot be disabled or deleted).
- **Log Analytics (2026 Update):** A feature that allows you to perform SQL-based analysis directly on your logs in a log bucket without exporting them to BigQuery.

## 3. Log Sinks (Exporting Logs)

Log Sinks allow you to export specific logs to other destinations for long-term storage or analysis.

- **Sink Destinations (Critical for Exam):**
  - **Cloud Storage:** Best for long-term, low-cost **archival** (years).
  - **BigQuery:** Best for **analytical** querying and SQL-based analysis.
  - **Pub/Sub:** Best for **streaming** logs to third-party tools or real-time processing.
  - **Another Log Bucket:** Routing logs to a centralized log bucket in another project.
- **Filters:** You use the **Logging Query Language (LQL)** to define which logs should be exported.

## 4. Log-based Metrics

Log-based Metrics allow you to create numerical metrics based on the content of your logs.

- **Counter Metrics:** Count the number of log entries that match a specific filter.
- **Distribution Metrics:** Extract numeric values from log entries.
- **Alerting:** You can create **Alerting Policies** in Cloud Monitoring based on these metrics.

## 5. Audit Logs

These are critical for security and compliance.

- **Admin Activity:** Records operations that modify the configuration of resources (always on).
  - Records are stored for 400 days. This is fixed, automatic, and free — **you cannot shorten or disable it**.
- **Data Access:** Records operations that read or write user-provided data. Must be manually enabled.
- **System Event:** Records administrative actions taken by Google Cloud.
- **Policy Denied:** Records when access is denied by a security policy.

## 6. Access Control (IAM)

- `roles/logging.admin`: Full control over all logging resources.
- `roles/logging.configWriter`: Permission to create sinks and log buckets.
- `roles/logging.viewer`: Permission to view logs in the Logs Explorer.
- `roles/logging.privateLogViewer`: Permission to view logs containing sensitive information.

## 7. Essential `gcloud` Commands

- **Read Logs:** `gcloud logging read "resource.type=gce_instance"`
- **Create a Sink:** `gcloud logging sinks create [SINK_NAME] storage.googleapis.com/[BUCKET_NAME] --log-filter="severity>=ERROR"`
- **List Sinks:** `gcloud logging sinks list`

## 8. Exam Tips

- **Export Choices:**
  - Archiving -> Cloud Storage
  - SQL Analysis -> BigQuery or Log Analytics
  - Real-time -> Pub/Sub
- **Retention vs. Sink:** Remember that logs in the Logs Explorer have a **retention period**.
