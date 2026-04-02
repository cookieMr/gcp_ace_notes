# Database Migration Service (DMS): ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Overview and Use Cases

Database Migration Service (DMS) is a managed, serverless service used to migrate databases to Google Cloud with minimal downtime.

- Target Destinations: Cloud SQL (MySQL, PostgreSQL, SQL Server) and AlloyDB for PostgreSQL.
- Migration Types:
  - Homogeneous: Source and destination are the same engine (e.g., MySQL to Cloud SQL for MySQL).
  - Heterogeneous: Source and destination are different (e.g., Oracle to Cloud SQL for PostgreSQL or AlloyDB).
- Key Benefit: Support for continuous (online) migrations using Change Data Capture (CDC).

## 2. Core Architecture Components

- Connection Profiles: Reusable configurations containing the connectivity information for the source and destination databases (IP, port, credentials).
- Migration Jobs: The specific task that defines the source connection profile, destination instance, and migration type.
- Private Connectivity: Ensuring secure data transfer via VPC Peering, Cloud VPN, Dedicated Interconnect, or Reverse SSH Tunnels.

## 3. The Migration Lifecycle

1. Assess: Evaluate the source database for compatibility. For heterogeneous migrations, use the integrated Conversion Workspace (powered by Gemini AI) to convert schema and code.
2. Create Connection Profiles: Define how DMS will talk to your source and destination.
3. Define Migration Job: Select the migration type (One-time or Continuous).
4. Run Validation: DMS performs pre-flight checks on connectivity, permissions, and configuration (e.g., binlog settings for MySQL).
5. Start Migration: DMS performs an initial full dump and then switches to continuous replication (if selected).
6. Promote: The final cutover step. It stops replication, disconnects the destination from the source, and makes the destination a standalone production database.

## 4. Source Prerequisites (Common Exam Topics)

- MySQL: Requires binary logging (binlog) enabled and server_id configured.
- PostgreSQL: Requires the pglogical extension (for versions < 14) or native logical replication (for versions 14+), and all tables must have primary keys.
- Oracle (Heterogeneous): Requires supplemental logging and specific user permissions for CDC.

## 5. Security and Monitoring

- Encryption: Supports SSL/TLS for data in transit and Customer-Managed Encryption Keys (CMEK) for data at rest.
- IAM Roles:
  - roles/datamigration.admin: Full control over DMS resources.
  - roles/datamigration.viewer: Read-only access to migration status.
- Monitoring: Integration with Cloud Monitoring to track Migration Lag (time difference between source and destination data).

## 6. Key Exam Tips and Gotchas

- DMS vs. M2VM: Use DMS for Databases (Cloud SQL/AlloyDB). Use Migrate to Virtual Machines (M2VM) for full lift-and-shift of VM disks.
- Promotion is Permanent: Once you Promote a migration job, it cannot be resumed. The destination is now the primary.
- Primary Keys: For PostgreSQL migrations, tables without primary keys will not be replicated during the continuous phase.
- Connectivity: If the source is on-premises, a VPN or Interconnect is highly recommended over the public internet. Use Reverse SSH Tunneling if you cannot modify firewall rules easily.

## 7. 2026 Updates

- Gemini in DMS: AI-assisted schema conversion for heterogeneous migrations (e.g., Oracle to PostgreSQL). It handles stored procedures, triggers, and functions.
- AlloyDB: Now a major target for DMS, especially for high-performance enterprise workloads.
- Gemini Code Assist: Use Gemini to refactor database-driven application code to match the new cloud database architecture.

[Back to README](README.md)
