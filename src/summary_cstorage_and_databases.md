# Storage & Databases

![Database & Storage](images/Databases-512-color.png)

_Image source: Google Cloud Documentation_

### **[Cloud Storage](./cloud_storage.md)**

Scalable object storage for unstructured data (images, backups, logs). Features multiple storage classes (**Standard**, **Nearline**, **Coldline**, **Archive**) and automated lifecycle management.

### **[Cloud SQL](./cloud_sql.md)**

Fully managed relational database (RDBMS) for **MySQL**, **PostgreSQL**, and **SQL Server**. Best for standard web applications and transactional (OLTP) workloads at a regional scale.

### **[AlloyDB](./alloy_db.md)**

AlloyDB is Google’s high-performance, PostgreSQL-compatible database. It disaggregates compute from storage for 4x faster performance, featuring a columnar engine for analytics and 99.99% availability.

### **[Cloud Spanner](./cloud_spanner.md)**

Enterprise-grade, globally distributed relational database. Provides **horizontal scalability** for both reads and writes with strong global consistency and up to 99.999% availability.

### **[Firestore / Datastore](./firestore.md)**

Serverless, NoSQL document database built for mobile, web, and IoT apps. Supports real-time synchronization, offline data access, and **ACID transactions** at the document level.

### **[Bigtable](./bigtable.md)**

High-performance, fully managed NoSQL wide-column database. Designed for **petabyte-scale**, low-latency workloads such as IoT telemetry, ad-tech, and financial data.

### **[BigQuery](./bigquery.md)**

Serverless, cost-effective enterprise data warehouse (EDW) for **OLAP** analytics. Uses a columnar architecture to query petabytes of data using standard SQL.

### **[Memorystore](./memorystore.md)**

Fully managed in-memory data store service for **Redis**, **Valkey**, and **Memcached**. Used for sub-millisecond latency caching, session management, and real-time analytics.

### **[Filestore](./filestore.md)**

Managed **NFS** file storage for applications that require a **POSIX-compliant** shared filesystem. Commonly used as shared storage for GKE pods and Compute Engine VMs.

### **[Persistent Disk](./persistent_disk.md)**

Persistent Disk is durable, high‑performance block storage for VM instances. It’s replicated for reliability, supports snapshots, resizing, and can detach/reattach across VMs.

