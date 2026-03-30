# GCP Cloud Logging: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud Logging Overview

Cloud Logging is a fully managed service that allows you to store, search, analyze, and alert on log data and events from Google Cloud.

- **Key Characteristics:**
  - **Unified:** Collects logs from all GCP services (Compute Engine, GKE, Cloud Run, etc.) and even multi-cloud/on-premises sources.
  - **Integrated:** Works seamlessly with Cloud Monitoring and Cloud Error Reporting.
  - **Retention:** Logs are kept for a specific period (standard is 30 days) and then automatically deleted.

## 2. Log Buckets

Logs are stored in **Log Buckets** (not Cloud Storage buckets).

- **Default Buckets:**
  - **\_Default:** For all standard logs (e.g., App Engine, Cloud Functions).
  - **\_Required:** For essential logs like Audit Logs (cannot be disabled or deleted).
- **Custom Buckets:** You can create custom log buckets with specific retention periods (up to 3650 days).

## 3. Log Sinks (Exporting Logs)

Log Sinks allow you to export specific logs to other destinations for long-term storage or analysis.

- **Sink Destinations (Critical for Exam):**
  - **Cloud Storage:** Best for long-term, low-cost **archival** (years).
  - **BigQuery:** Best for **analytical** querying and SQL-based analysis.
  - **Pub/Sub:** Best for **streaming** logs to third-party tools (like Splunk) or real-time processing.
  - **Another Log Bucket:** Routing logs to a centralized log bucket in another project.
- **Filters:** You use the **Logging Query Language (LQL)** to define which logs should be exported (e.g., "Export all error logs from us-central1").

## 4. Log-based Metrics

Log-based Metrics allow you to create numerical metrics based on the content of your logs.

- **Counter Metrics:** Count the number of log entries that match a specific filter (e.g., "Count the number of 404 errors").
- **Distribution Metrics:** Extract numeric values from log entries (e.g., "Extract the latency values from web server logs").
- **Alerting:** You can create **Alerting Policies** in Cloud Monitoring based on these metrics.

## 5. Audit Logs

These are critical for security and compliance.

- **Admin Activity:** Records operations that modify the configuration of resources (always on, no charge).
- **Data Access:** Records operations that read or write user-provided data (e.g., reading a GCS file). Must be manually enabled (except for BigQuery) and incurs costs.
- **System Event:** Records administrative actions taken by Google Cloud.
- **Policy Denied:** Records when a user or service is denied access by a security policy.

## 6. Access Control (IAM)

- `roles/logging.admin`: Full control over all logging resources.
- `roles/logging.configWriter`: Permission to create sinks and log buckets.
- `roles/logging.viewer`: Permission to view logs in the Logs Explorer.
- `roles/logging.privateLogViewer`: Permission to view logs containing sensitive information (like certain audit logs).

## 7. Essential gcloud Commands

- **Read Logs:** `gcloud logging read "resource.type=gce_instance"`
- **Create a Sink:**
  `gcloud logging sinks create [SINK_NAME] storage.googleapis.com/[BUCKET_NAME] --log-filter="severity>=ERROR"`
- **List Sinks:** `gcloud logging sinks list`
- **Create Log-based Metric:**
  `gcloud logging metrics create [METRIC_NAME] --description="Count 404s" --log-filter="textPayload:404"`

## 8. Exam Tips

- **Export Choices:**
  - Archiving -> Cloud Storage.
  - SQL Analysis -> BigQuery.
  - Third-party/Real-time -> Pub/Sub.
- **Retention vs. Sink:** Remember that logs in the Logs Explorer have a **retention period** (30 days default). If you need them for longer, you must set up a **Sink** or a **Custom Bucket**.
- **Exclusion Filters:** Use these to exclude specific logs from being stored to save on costs.
- **Structured Logs:** Google Cloud services often produce structured logs in JSON format, which are easier to filter and query.

[Back to root](./README.md)
