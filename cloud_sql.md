# GCP Cloud SQL: Associate Cloud Engineer (ACE) Study Guide

[Back to root](./README.md)

## 1. Core Overview

Cloud SQL is a fully managed relational database service (RDBMS) on Google Cloud.

- **Supported Database Engines:** MySQL, PostgreSQL, and SQL Server.
- **Use Cases:** Web frameworks, structured data, existing applications that require standard SQL, OLTP (Online Transaction Processing) workloads.
- **Limitations to know for ACE:** Maximum storage capacity is typically around 64 TB (varies slightly by engine/configuration). If you need global scalability or database sizes exceeding this limit, the exam expects you to choose **Cloud Spanner**.

---

## 2. High Availability (HA) and Replication

Understanding the difference between HA and Read Replicas is heavily tested on the ACE exam.

### High Availability (HA)

- **Purpose:** Protection against zone failures. Provides reliability, not performance scaling.
- **Architecture:** Regional configuration. It provisions a Primary instance in one zone and a Standby instance in another zone within the same region.
- **Replication Type:** Synchronous replication at the block level using persistent disks.
- **Failover:** Automatic. If the primary zone goes down, the standby takes over the primary IP address.

### Read Replicas

- **Purpose:** Read performance scaling (offloading read queries from the primary instance).
- **Architecture:** Can be in the same region or a different region (Cross-Region Read Replica).
- **Replication Type:** Asynchronous replication.
- **Failover:** Manual. You must manually "promote" a read replica to become a standalone primary instance if you want to use it for disaster recovery.

---

## 3. Backups and Recovery

- **Automated Backups:** Taken daily within a configurable backup window. Retained for up to 365 days (default is usually 7).
- **On-Demand Backups:** Taken manually at any time. They are not deleted automatically; you must delete them manually.
- **Point-in-Time Recovery (PITR):** Allows you to restore an instance to a specific fraction of a second. Requires binary logging (MySQL) or write-ahead logging (PostgreSQL) to be enabled.
- **Cloning:** You can clone a Cloud SQL instance to create an exact, independent copy of the database.

---

## 4. Scaling

- **Vertical Scaling:** Increasing the machine type (vCPUs and RAM). **Requires a restart** of the database instance.
- **Horizontal Scaling:** Using Read Replicas to scale read capacity. Cloud SQL does not natively horizontally scale for write operations (you need Cloud Spanner for that).
- **Storage Auto-Increase:** You can enable this feature so Cloud SQL automatically adds storage capacity as you approach your limit.
- **Crucial Exam Fact:** Cloud SQL storage can scale up, but it **cannot scale down**.

---

## 5. Security and Networking

- **Private IP:** Instances can have a private, internal IP. Requires **Private Services Access** (VPC Peering) between your VPC and the Google-managed producer network.
- **Public IP with Authorized Networks:** If using a public IP, you must whitelist source IPs in "Authorized Networks."
- **Cloud SQL Auth Proxy (formerly Cloud SQL Proxy):** 
  - **Exam Tip:** This is the **Gold Standard** for secure connections. It uses IAM for authentication and automatically handles SSL/TLS. You do NOT need to whitelist IP addresses when using the proxy.
- **IAM Authentication:** Allows users/service accounts to log in using their Google Cloud identity. This is more secure than managing individual database passwords.

---

## 6. Maintenance

- **Maintenance Windows:** Cloud SQL requires routine maintenance for underlying infrastructure and database engine updates.
- **Configuration:** You define a specific day of the week and time of day (the maintenance window) when Google is allowed to perform these updates.
- **Impact:** Maintenance usually results in a brief period of downtime.

---

## 7. Decision Tree for the ACE Exam (When to choose Cloud SQL)

When reading an exam question, use these identifiers:

- Structured data / Relational? -> **Cloud SQL** or **Spanner**.
- Local/Regional scale? -> **Cloud SQL**.
- Global scale or massive writes? -> **Cloud Spanner**.
- Petabytes of data / Data Warehousing / OLAP? -> **BigQuery**.
- Unstructured data / NoSQL? -> **Cloud Firestore / Datastore** (document) or **Cloud Bigtable** (wide-column, low latency, IoT).

## 8. Migration and Administrative Tasks

- **Database Migration Service (DMS):** The primary tool for "Lift and Shift" migrations from on-premises or other clouds to Cloud SQL (supports MySQL and PostgreSQL).
- **Import/Export:** 
  - **SQL dumps:** Standard way to move data.
  - **CSV files:** Supported for flat data.
  - **Storage:** You MUST store the SQL/CSV file in a **Cloud Storage (GCS)** bucket first before importing it into Cloud SQL.
- **Service Account Permissions:** To import/export, the Cloud SQL **Service Account** (not your user account) must have `roles/storage.objectViewer` or `roles/storage.objectAdmin` on the GCS bucket.
- **Read Replica Limit:** You can create up to **10** read replicas per primary instance.

[Back to root](./README.md)
