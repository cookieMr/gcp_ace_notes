# GCP Cloud Spanner: Associate Cloud Engineer (ACE) Study Guide

[Back to root](./README.md)

## 1. Core Overview

- **Database Type:** Fully managed, enterprise-grade relational database (RDBMS).
- **Key Features:** Horizontal scalability, strong global consistency, and high availability (up to 99.999% SLA).
- **Language:** Supports Standard SQL (Google Standard SQL and PostgreSQL-dialect).

## 2. When to Choose Cloud Spanner (Exam Scenarios)

- **Massive Scale:** Your relational database exceeds Cloud SQL storage limits (typically > 64 TB) or requires tens of thousands of reads/writes per second.
- **Horizontal Scaling:** You need a relational database that can scale horizontally (by adding more nodes) for both reads _and_ writes.
- **Global Geography:** You need a globally distributed database with strong consistency across regions (e.g., global financial ledger, worldwide inventory system, global supply chain).

## 3. Cloud Spanner vs. Cloud SQL (Crucial for ACE)

The ACE exam frequently tests your ability to choose between these two services.

- **Cloud SQL:** Regional, scales vertically for write operations (which requires downtime to resize the machine), and has storage limits. Best for standard web applications, local/regional scale, and "lift and shift" of standard MySQL/PostgreSQL workloads.
- **Cloud Spanner:** Global or multi-regional, scales horizontally for both read and write operations (zero downtime for scaling), and offers virtually unlimited storage. Best for massive, global-scale relational data.

## 4. Architecture and Compute

- **Processing Units (PUs) and Nodes:** Compute capacity is measured in PUs or nodes. 1 node = 1000 PUs.
- **Scaling & Storage Limits (Tested):**
  - **Zero Downtime:** Scaling nodes/PUs up or down is instantaneous and happens while the database is serving traffic.
  - **Storage Limit:** Each 1,000 PUs (1 node) supports up to **4 TB** of storage. If your database grows beyond 4 TB, you MUST add more nodes even if CPU usage is low.
- **Interleaved Tables:** A unique Spanner feature where a "child" table's rows are physically stored with the "parent" table's rows (e.g., `Invoices` interleaved in `Customers`). This drastically improves performance for related data joins.
- **High Availability (SLA):** 
  - **Regional:** 99.99% availability.
  - **Multi-regional:** 99.999% availability (the famous "five nines").

## 5. IAM and Security

- **Access Control:** IAM roles can be granted at the project, instance, or database level.
- **Common Roles:** 
  - `roles/spanner.admin`: Full control of all Spanner resources.
  - `roles/spanner.databaseAdmin`: Manage databases and schema, but cannot create/delete the Spanner instance itself.
  - `roles/spanner.databaseReader`: Read data and schema.
  - `roles/spanner.viewer`: View instance and database metadata (read-only).
- **Security Features:** Integrates with **Cloud Audit Logs** (Data Access and Admin Activity) and supports **CMEK** (Customer-Managed Encryption Keys).

## 6. Backups and Recovery

- **Point-in-Time Recovery (PITR):** Allows you to read data from a specific microsecond in the past to recover from accidental deletions or bad writes. The maximum retention period for PITR is 7 days.
- **Backups:** You can take on-demand backups of your database. These backups are retained for up to 1 year and are stored in the same geographic location as the database instance.

## 7. Interacting with Cloud Spanner (CLI)

For the ACE exam, know the `gcloud spanner` command group:
- `gcloud spanner instances list`: List all instances in a project.
- `gcloud spanner databases create [DB_NAME] --instance=[INSTANCE_NAME]`: Create a new database.
- `gcloud spanner instances update [INSTANCE_NAME] --nodes=[COUNT]`: Scale an instance horizontally.

[Back to root](./README.md)
