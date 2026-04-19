# Migration & Hybrid

![Migration & Hybrid](images/Migration-512-color.png)

_Image source: Google Cloud Documentation_

### **[Migrate to Virtual Machines](./migrate_to_virtual_machines.md)**

Lift-and-shift solution for migrating physical or virtual machines to Compute Engine. Supports VMware vSphere, AWS EC2, and Azure VMs. Uses continuous replication to minimize downtime while the source VM remains running.

### **[Database Migration Service](./database_migration_service.md)**

Fully managed service for migrating databases to Cloud SQL (MySQL, PostgreSQL, SQL Server) or AlloyDB. Supports homogeneous and heterogeneous migrations with continuous replication using Change Data Capture (CDC).

### **[Storage Transfer Service](./storage_transfer_service.md)**

Managed service for transferring data into Cloud Storage from AWS S3, Azure Blob, HTTP/HTTPS, on-premises, or between GCS buckets. Handles scheduling and error handling without managing VMs.

### **[Transfer Appliance](./transfer_appliance.md)**

High-capacity offline data migration appliance (40TB, 300TB+). Best for large datasets (>20TB) where internet bandwidth is limited. Bypasses slow connections by physically shipping data to Google.