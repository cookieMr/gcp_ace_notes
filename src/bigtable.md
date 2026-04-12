# Cloud Bigtable: ACE Exam Study Guide (2026)

![Bigtable](images/bigtable.png)

_Image source: Google Cloud Documentation_

## 1. Core Overview

- **Database Type:** Fully managed, wide-column NoSQL database.
- **Scale:** Designed for massive datasets (Terabytes to Petabytes).
- **Performance:** Offers single-digit millisecond latency and extremely high throughput for both read and write operations.
- **Compatibility:** Natively exposes an Apache HBase API.
- **Gemini Integration:** Gemini can provide assistance with schema design, row key optimization, and performance troubleshooting.

## 2. When to Choose Cloud Bigtable (Exam Scenarios)

- **Time-Series Data:** IoT sensor readings, server telemetry, and monitoring metrics.
- **High Throughput / Low Latency:** Ad-tech, financial market data, and massive multiplayer game state or analytics.
- **Rule of Thumb:** If an exam question explicitly mentions "sub-millisecond latency," "petabytes of data," or "HBase compatibility," Bigtable is highly likely the correct answer.

## 3. When NOT to Choose Cloud Bigtable

- **Relational Data:** It does not support standard SQL queries, complex joins, or multi-row transactions.
- **Small Datasets:** It is not cost-effective or necessary for datasets under 1 Terabyte. Cloud Firestore, Cloud SQL, or Cloud Spanner are better suited for smaller workloads.

## 4. Architecture and Performance

- **Compute and Storage Separation:** Nodes handle compute, while data resides on Colossus. This allows you to scale nodes up or down with zero downtime without migrating data.
- **Storage Types:**
  - **SSD:** Default choice. For high-performance, low-latency workloads.
  - **HDD:** For massive amounts of data (>10 TB) where latency is not critical (e.g., batch processing).
  - **Immutability:** You _cannot change_ the storage type (SSD/HDD) after the instance is created.
- **Row Key Design (Tested):**
  - **Avoid Hotspotting:** Do NOT use sequential IDs or timestamps as the start of a row key.
  - **Best Practice:** Use "tall and skinny" tables. Use hashed values, reverse domain names (e.g., `com.google.cloud`), or salted keys to ensure data is distributed evenly across nodes.

## 5. Command Line Operations

- **The `cbt` Tool:** While you use `gcloud` to manage the Bigtable instances and clusters, the ACE exam expects you to know that you interact with the actual tables and data using the `cbt` command-line tool.
- **Common Commands:** `cbt createtable`, `cbt read`, `cbt set`.

## 6. High Availability and Replication

- **Replication:** Bigtable provides high availability by replicating data across multiple clusters in different zones or regions.
- **App Profiles:** Used to manage how your applications connect to a cluster.
  - **Single-Cluster Routing:** Directs traffic to one cluster (consistent, but no automatic failover).
  - **Multi-Cluster Routing:** Automatically fails over to the nearest available cluster (High Availability).

## 7. Administrative Tasks and Scaling

- **Scaling:** You can increase or decrease the number of nodes in a cluster via the Console or `gcloud` while the cluster is serving traffic (zero downtime).
- **Monitoring:** Use _Key Visualizer_ (a tool within the GCP Console) to identify _hotspots_ and troubleshoot performance issues visually.
- **Backups:** Bigtable allows you to take _Backups_ of tables. These are stored within the Bigtable service (in the same region), NOT in Cloud Storage. They can only be used to _Restore_ to a new table.

## 8. External Links

- [Cloud Bigtable - The Cloud Girl](https://www.thecloudgirl.dev/database/cloud-bigtable)
- [Introduction to Databases - The Cloud Girl](https://www.thecloudgirl.dev/database/introduction-to-databases)
- [Which Database should I Use - The Cloud Girl](https://www.thecloudgirl.dev/database/which-database-should-i-use)
