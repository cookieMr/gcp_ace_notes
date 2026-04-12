# Cloud Spanner: ACE Exam Study Guide (2026)

![Cloud Spanner](images/google-cloud-spanner.png)

_Image source: [Vecta.io](https://vecta.io/symbols/4/google-cloud-platform/44/google-cloud-spanner)_

## 1. Core Overview

- **Database Type:** Fully managed, enterprise-grade relational database (RDBMS) with global scale.
- **Key Features:** Horizontal scalability, strong global consistency, and high availability (up to 99.999% SLA).
- **Language:** Supports Standard SQL (Google Standard SQL) and PostgreSQL-dialect.

## 2. When to Choose Cloud Spanner (Exam Scenarios)

- **Massive Scale:** Your relational database exceeds Cloud SQL storage limits (typically > 64 TB) or requires tens of thousands of reads/writes per second.
- **Horizontal Scaling:** You need a relational database that can scale horizontally (by adding more nodes/PUs) for both reads and writes.
- **Global Geography:** You need a globally distributed database with strong consistency across regions (e.g., global financial ledger, worldwide inventory system).
- **Graph and Relational:** With **Spanner Graph**, you can now store and query graph data alongside relational data in the same database using the ISO GQL standard.

## 3. Cloud Spanner vs. Cloud SQL vs. AlloyDB

The ACE exam frequently tests your ability to choose between these two services.

## Cloud SQL vs AlloyDB vs Cloud Spanner (ACE Summary)

| Feature           | Cloud SQL                               | AlloyDB                              | Cloud Spanner                             |
| ----------------- | --------------------------------------- | ------------------------------------ | ----------------------------------------- |
| **Scope**         | Regional                                | Regional                             | Global / Multi‑regional                   |
| **Scaling**       | Vertical (downtime)                     | Horizontal read scaling (read pools) | Horizontal read + write scaling           |
| **Performance**   | Standard                                | Much faster than Cloud SQL           | Highest, globally consistent              |
| **Compatibility** | MySQL / PostgreSQL / SQL Server         | PostgreSQL‑compatible                | Spanner SQL                               |
| **Availability**  | HA optional (regional)                  | HA with primary + read pools         | Built‑in global HA                        |
| **Storage**       | Limited                                 | High-performance, auto‑scaling       | Virtually unlimited                       |
| **Best For**      | Typical web apps, standard DB workloads | High‑performance transactional apps  | Massive, global, mission‑critical systems |

## 4. Architecture and Compute

- **Processing Units (PUs) and Nodes:** Compute capacity is measured in PUs or nodes. 1 node = 1000 PUs.
- **Scaling and Storage Limits (2026 Standards):**
  - **Zero Downtime:** Scaling nodes/PUs up or down is instantaneous and happens while the database is serving traffic.
  - **Storage Limit:** Each 1,000 PUs (1 node) now supports up to **10 TB** of storage in modern configurations. If your database grows beyond this, you MUST add more nodes even if CPU usage is low.
- **Interleaved Tables:** A unique Spanner feature where a child table's rows are physically stored with the parent table's rows. This drastically improves performance for related data joins by ensuring data is co-located on the same split.
- **High Availability (SLA):**
  - **Regional:** 99.99% availability.
  - **Multi-regional:** 99.999% availability (the famous five nines).

## 5. IAM and Security

- **Access Control:** IAM roles can be granted at the project, instance, or database level.
- **Common Roles:**
  - `roles/spanner.admin`: Full control of all Spanner resources.
  - `roles/spanner.databaseAdmin`: Manage databases and schema, but cannot create/delete the Spanner instance itself.
  - `roles/spanner.databaseReader`: Read data and schema.
  - `roles/spanner.viewer`: View instance and database metadata (read-only).
- **Security Features:** Integrates with **Cloud Audit Logs** and supports **CMEK** (Customer-Managed Encryption Keys).

## 6. Backups and Recovery

- **Point-in-Time Recovery (PITR):** Allows you to read data from a specific microsecond in the past. The maximum retention period for PITR is 7 days.
- **Backups:** You can take on-demand backups of your database. These backups are retained for up to 1 year and are stored in the same geographic location as the database instance.
- **Export/Import:** Uses Dataflow to move data between Spanner and Cloud Storage (Avro or CSV formats).

## 7. Interacting with Cloud Spanner (CLI)

For the ACE exam, know the `gcloud spanner` command group:

- `gcloud spanner instances list`: List all instances in a project.
- `gcloud spanner databases create [DB_NAME] --instance=[INSTANCE_NAME]`: Create a new database.
- `gcloud spanner instances update [INSTANCE_NAME] --nodes=[COUNT]`: Scale an instance horizontally.

## 8. External Links

- [Youtube - Andrew Brown - GCP ACE](https://www.youtube.com/watch?v=OlAmyf8_4O4&t=14504s)
- [Cloud Spanner - The Cloud Girl](https://www.thecloudgirl.dev/database/cloud-spanner)
- [Introduction to Databases - The Cloud Girl](https://www.thecloudgirl.dev/database/introduction-to-databases)
- [Which Database should I Use - The Cloud Girl](https://www.thecloudgirl.dev/database/which-database-should-i-use)
