# Compute Engine: ACE Exam Study Guide (2026)

## 1. Compute Engine Overview

Compute Engine is Google Cloud's Infrastructure as a Service (IaaS) offering, providing customizable Virtual Machines (VMs).

- **Machine Families (2026 Standards):**
  - **General-purpose:** Best price-performance. Includes **E2**, **N2**, and the new **N4** (optimized for modern workloads with flexible sizing).
  - **Compute-optimized:** High performance per core. Includes **C2**, **C3**, and **C4** (the latest generation for high-performance computing).
  - **Memory-optimized:** High memory/vCPU ratio. Includes **M1**, **M2**, and **M3**.
  - **Accelerator-optimized:** GPUs attached (e.g., **A2**, **A3**).
- **Gemini Integration:** Gemini in Compute Engine provides AI-assisted instance type selection, performance optimization tips, and troubleshooting for VM startup issues.

## 2. Pricing and Discounts

- **Cost of Stopped VMs:** If you stop a VM, you stop paying for CPU and RAM, but you still pay for attached Persistent Disks and any reserved Static External IPs.
- **Sustained Use Discounts (SUD):** Automatic discounts for running instances for a significant portion of the month (N1, N2).
- **Committed Use Discounts (CUD):** 1 or 3-year commitment for a predictable workload.
- **Spot VMs:** Up to 91% discount. These can be terminated by Google at any time with a 30-second notice. Best for fault-tolerant, stateless batch jobs.

## 3. Instance Templates and Managed Instance Groups (MIGs)

- **Instance Templates:** Immutable resources that define VM properties (machine type, image, labels). Used to create MIGs.
- **Managed Instance Groups (MIGs):** A collection of identical VMs that offer high availability and scalability.
  - **Auto-healing:** Automatically recreates VMs that fail health checks.
  - **Auto-scaling:** Dynamically adds or removes VMs based on CPU utilization, load balancing capacity, or custom metrics.
  - **Regional MIGs:** Highly recommended for production as they distribute VMs across multiple zones in a region.

## 4. Persistent Disks, Snapshots and Images

- **Persistent Disks (PD):** Durable network storage. You can resize a disk up but never down.
- **Snapshots:** Incremental backups of disks, stored globally. Best for disaster recovery.
- **Custom Images:** A "Gold Master" boot disk with your OS and software pre-installed. Best for consistent deployments in MIGs.
- **Local SSD:** Physical drives attached directly to the host. Data is ephemeral and lost if the VM is stopped or deleted.

## 5. Sole-Tenant Nodes

- **Definition:** Physical Compute Engine servers dedicated entirely to hosting your project's VMs.
- **Use Cases:** Compliance, security isolation, and specific licensing (BYOL) that requires physical hardware.
- **Node Groups:** Nodes are organized into groups, and you use node affinity labels to ensure VMs boot on these specific servers.

## 6. Connecting to Instances

- **SSH Access:** `gcloud compute ssh [VM_NAME]`.
- **Identity-Aware Proxy (IAP):** The recommended way to SSH into a VM that has **no external IP address**.
- **Firewall Rule for IAP:** You must allow ingress traffic from the IP range `35.235.240.0/20` on port 22.

## 7. Service Accounts and Metadata

- **Service Accounts:** VMs use these to authenticate to other Google Cloud services (GCS, BigQuery). Always use custom service accounts with "Least Privilege" for production.
- **Metadata:** Used to pass configuration data. Startup scripts are automated scripts that run every time the VM boots.
- **Metadata Server:** Accessible at `http://metadata.google.internal/computeMetadata/v1/`.

## 8. Essential gcloud Commands

- **Create a VM:** `gcloud compute instances create [NAME] --zone=[ZONE] --machine-type=[TYPE]`
- **Resize a MIG:** `gcloud compute instance-groups managed resize [NAME] --size=[NEW_SIZE]`
- **List Instances:** `gcloud compute instances list`

## 9. Exam Tips

- **Preemption:** If a Spot VM is terminated, it is a preemption, not a system crash.
- **Zonal vs. Regional MIG:** Choose Regional MIG for the highest availability.
- **Metadata Header:** Requests to the metadata server require the header `Metadata-Flavor: Google`.
- **Machine Type Selection:** If a question asks for the best cost-performance for a general workload, consider **E2** or **N4**. For high-performance databases, consider **C4** or **M3**.
