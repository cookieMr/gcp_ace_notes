# GCP Compute Engine: ACE Exam Study Guide

[Back to root](./README.md)

## 1. Compute Engine Overview

Compute Engine is Google Cloud's Infrastructure as a Service (IaaS) offering, providing customizable Virtual Machines (VMs).

- **Machine Families:**
  - **General-purpose:** Best price-performance. Includes **E2** (cost-effective, no GPU/local SSD support), **N2/N2D**, and **T2D (Tau)** (best performance/price for scale-out).
  - **Compute-optimized:** High performance per core (e.g., **C2, C3**). Ideal for gaming, HPC, and media transcoding.
  - **Memory-optimized:** High memory/vCPU ratio (e.g., **M1, M2, M3**). Best for SAP HANA and large in-memory DBs.
  - **Accelerator-optimized:** GPUs attached (e.g., **A2, A3**). Best for ML and parallel processing.
- **Pricing and Discounts:**
  - **Cost of Stopped VMs:** _Exam Tip:_ If you stop a VM, you **stop paying for CPU/RAM**, but you **still pay** for attached Persistent Disks and any reserved Static External IPs.
  - **Sustained Use Discounts (SUD):** Automatic (N1, N2, M1).
  - **Committed Use Discounts (CUD):** 1 or 3-year commitment (VCPUs/RAM).
- **Spot VMs:** Up to 91% discount. Can be terminated at any time.
  - _Exam Tip:_ Use for fault-tolerant, stateless batch jobs. If a Spot VM is terminated, it is a **preemption**, not a crash.

## 2. Instance Templates

An instance template is an API resource used to define VM instance properties (machine type, boot disk image, zone, labels, network interfaces).

- **Immutability:** Instance templates are **immutable**. Once created, they cannot be updated or changed. To make a change, you must create a new template and apply it to your instance group.
- **Global Resource:** Templates are global resources, meaning a single template can be used to create VMs in any zone or region across your project.
- **Primary Use Case:** Used almost exclusively to create VMs in Managed Instance Groups (MIGs).

## 3. Managed Instance Groups (MIGs)

MIGs manage a collection of identical VMs created from a single instance template. This is how you achieve high availability and scalability in Compute Engine.

- **Auto-healing:** MIGs maintain the desired number of instances. If a VM crashes or fails a health check, the MIG automatically recreates it.
- **Auto-scaling:** MIGs can dynamically add or remove VMs based on specific metrics:
  - Average CPU utilization.
  - HTTP load balancing capacity.
  - Cloud Monitoring custom metrics.
- **Zonal vs. Regional MIGs:**
  - _Zonal:_ All VMs are in a single zone.
  - _Regional:_ VMs are distributed across multiple zones within the same region, protecting against single-zone failures (highly recommended for production).
- **Updating MIGs:** Updates are done via **Rolling Updates**. You apply a new instance template, and the MIG replaces the old VMs with new ones gradually to ensure zero downtime.

## 4. Persistent Disks, Snapshots & Images

Persistent Disks (PD) are durable network storage devices.

- **Types of PDs:** Standard (HDD), Balanced (SSD), Performance/Extreme (SSD).
- **Disk Operations:**
  - You can resize a disk **up** on the fly, but never **down**.
  - A disk can be attached to multiple VMs in **Read-Only** mode (multi-writer requires specific setup).
- **Snapshots vs. Custom Images:**
  - **Snapshots:** Used for **backups and disaster recovery**. They are incremental and stored globally.
  - **Custom Images:** Used to create a **"Gold Master"** boot disk with your OS and software pre-installed. Best for consistent deployments in MIGs.
  - _Exam Tip:_ To create a Custom Image from a running VM, it is best practice to stop the VM first to ensure data consistency.

## 5. Local SSD

Local SSDs are physical solid-state drives attached directly to the server hosting your VM instance.

- **Performance:** Offers extremely high IOPS and very low latency compared to standard Persistent Disks because they are not network-attached.
- **Ephemeral Nature:** Data on a Local SSD is **ephemeral** (temporary). If you stop or delete the VM instance, the data on the Local SSD is permanently lost.
- **Exam Constraints:**
  - You cannot use a Local SSD as a boot disk.
  - Always fixed at 375 GB increments per attached drive (up to 24 partitions, totaling 9 TB).
  - Best for scratch disks, temporary caching, or intermediate processing data where data loss is acceptable.

## 6. Sole-Tenant Nodes

A sole-tenant node is a physical Compute Engine server dedicated entirely to hosting only your project's VMs.

- **Use Cases:**
  - **Compliance/Security:** Strict regulatory requirements demanding physical isolation from other Google Cloud customers.
  - **Licensing (BYOL):** "Bring Your Own License" scenarios (like Windows Server or SQL Server) that require hardware-based licensing tied to physical cores or sockets.
  - **Performance:** Avoiding "noisy neighbors" on shared hardware.
- **Node Groups:** Nodes are organized into node groups. You can specify a node affinity label in your instance template to ensure specific VMs only boot on your sole-tenant nodes.

## 7. Connecting to Instances & Security

How you access your VMs is a major exam topic.

- **SSH Access:**
  - **Standard:** `gcloud compute ssh [VM_NAME]`. Requires port 22 to be open and an external IP.
  - **IAP (Identity-Aware Proxy):** _High Probability Exam Question._ Allows you to SSH into a VM that has **no external IP address**.
  - **Firewall Rule for IAP:** You must allow ingress traffic from the IP range `35.235.240.0/20` on port 22.
- **Network Tags:** Apply tags (e.g., `web-server`) to VMs to selectively apply firewall rules (e.g., "Allow port 80 to instances with tag `web-server`").

## 8. VM Lifecycle & Operations

- **States:**
  - **Provisioning/Staging:** Resources are being acquired.
  - **Running:** VM is active.
  - **Stopping:** Transitioning to terminated.
  - **Terminated:** VM is stopped. No CPU/RAM charges.
- **Operations:**
  - **Reset:** Like a hard power cycle (wipes memory, does not stop billing).
  - **Stop:** Shuts down the OS and stops CPU/RAM billing.
  - **Suspend:** Saves the instance state to disk (like closing a laptop). You pay for the storage of the saved state.

## 9. Service Accounts & Metadata

- **Service Accounts:** VMs use Service Accounts to authenticate to other GCP services (GCS, BigQuery) without needing to store secret keys in the code.
  - _Exam Tip:_ The **Default Compute Service Account** has broad "Editor" permissions by default. In production, use a **Custom Service Account** with "Least Privilege".
- **Metadata:** Used to pass configuration data to the VM.
  - **Startup Scripts:** Automated scripts that run every time the VM boots.
  - **Metadata Server:** Accessible from within the VM at `http://metadata.google.internal/computeMetadata/v1/`.
  - Requires the header: `Metadata-Flavor: Google`.

## 10. Essential gcloud Commands

- Create a VM: `gcloud compute instances create [NAME] --zone=[ZONE] --machine-type=[TYPE]`
- Resize a MIG: `gcloud compute instance-groups managed resize [NAME] --size=[NEW_SIZE]`
- Create a Template: `gcloud compute instance-templates create [NAME] --source-instance=[VM]`

[Back to root](./README.md)
