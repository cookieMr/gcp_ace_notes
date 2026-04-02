# Migrate to Virtual Machines (M2VM): ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Overview and Use Cases

Migrate to Virtual Machines is a lift-and-shift (Rehost) solution used to migrate physical or virtual machines into Compute Engine.

- Supported Sources: VMware vSphere (on-premises), AWS (EC2), and Azure (VMs).
- Key Benefit: Minimal downtime migration. Data is replicated in the background while the source VM remains running.
- Target: All migrated workloads land as Compute Engine (GCE) instances.

## 2. Core Architecture Components

- Migration Center: The unified platform for discovery, assessment, and planning. It integrates with Gemini to provide AI-driven migration recommendations.
- Migrate Connector: An appliance (OVA for VMware) installed on the source environment to facilitate discovery and data replication to GCP.
- Host Project: The project where you enable the Migration API and manage the migration process.
- Target Project: The project where the final Compute Engine instances will be created and run.
- Replication: The process of continuously syncing data from the source to a Cloud Storage bucket or Persistent Disk in GCP.

## 3. The Migration Lifecycle

The order of operations is a common exam topic:

1. Assess: Use Migration Center to discover inventory, estimate costs, and check OS compatibility. Gemini can assist in analyzing large-scale inventories to prioritize workloads.
2. Plan: Group VMs into Migration Groups to manage them together (e.g., all VMs for a specific application).
3. Deploy (Replicate): Start the replication of data from source to GCP.
4. Test Clone: Create a sandbox instance in GCP to verify the VM boots and the application works without affecting the source VM.
5. Cutover:
   - Shuts down the source VM.
   - Performs a final data sync.
   - Starts the production VM in Compute Engine.
6. Finalize/Detach: Once the migration is verified, the connection to the source is severed, and the migration is marked as complete.

## 4. Networking and Security

- Connectivity: Requires a stable connection between the source and GCP. Cloud Interconnect is preferred for large migrations; Cloud VPN is used for smaller ones.
- Firewalls: Port 443 (HTTPS) must be open for the Migrate Connector to communicate with GCP APIs.
- IAM Roles:
  - vmmigration.admin: Full control over the migration process.
  - vmmigration.viewer: Read-only access to migration status.
  - The Service Account used by the connector needs storage.admin and compute.admin permissions.

## 5. Key Exam Tips and Gotchas

- Test Clone vs. Cutover: A Test Clone does NOT stop the source VM. A Cutover DOES stop the source VM.
- Downtime: The only downtime occurs during the Cutover phase (usually minutes), as the VM must be rebooted in the new environment.
- OS Support: Ensure the OS is supported by GCP (e.g., specific versions of RHEL, CentOS, Debian, Ubuntu, or Windows Server).
- IP Addresses: By default, VMs get new internal IP addresses in GCP unless you manually configure the VPC/Subnet to match the source.
- Machine Types: In 2026, consider using N4 or C4 machine types for migrated workloads to optimize for performance and cost.

## 6. Comparison with Other Tools

- Database Migration Service (DMS): Use for Cloud SQL migrations, NOT for full VMs.
- Storage Transfer Service: Use for moving large amounts of data (e.g., S3 to GCS), NOT for OS/boot disks.
- Anthos Migrate: Used specifically for migrating VMs directly into Containers (GKE).
- Gemini Code Assist: Use to modernize application code after the lift-and-shift to take advantage of cloud-native features.

[Back to README](README.md)
