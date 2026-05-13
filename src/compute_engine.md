# Compute Engine: ACE Exam Study Guide (2026)

<figure>
  <img src="images/compute_engine.png" alt="Compute Engine Icon" width=200>
  <figcaption><center>Compute Engine<br><i>Image source: Google Cloud Documentation</i></center></figcaption>
</figure>

## 1. Compute Engine Overview

Compute Engine is Google Cloud's _Infrastructure as a Service (IaaS)_ offering, providing customizable Virtual Machines (VMs).

- Machine Families (2026 Standards)
  - **General-purpose:** Best price-performance. Includes **E2**, **N2**, and the new **N4** (optimized for modern workloads with flexible sizing).
  - **Compute-optimized:** High performance per core. Includes **C2**, **C3**, and **C4** (the latest generation for high-performance computing).
  - **Memory-optimized:** High memory/vCPU ratio. Includes **M1**, **M2**, and **M3**.
  - **Accelerator-optimized:** GPUs attached (e.g., **A2**, **A3**).
  - **Custom Machine Types:** Variable vCPU and RAM configurations when preset types don't fit your needs.

## 2. Pricing and Discounts

- **Cost of Stopped VMs:** If you stop a VM, you stop paying for CPU and RAM, but you still pay for attached _Persistent Disks_ and any reserved _Static External IPs_.
- **External IPs:**
  - **Ephemeral:** Automatically assigned when VM starts, released when VM stops/deletes
  - **Static:** Reserved IP address that persists independently of VM lifecycle (incurs charges when unused & survives instance stop/start/restart actions)
- **Sustained Use Discounts (SUD):** Automatic discounts for running instances for a significant portion of the month (N1, N2).
- **Committed Use Discounts (CUD):** 1 or 3-year commitment for a predictable workload.
- **Spot VMs:** Up to 91% discount. These can be terminated by Google at any time with a 30-second notice. Best for fault-tolerant, stateless batch jobs.
  - Use **shutdown scripts** to handle graceful termination and save state.
  - Preemption of a Spot VM is called a _preemption_, not a system crash.
- **Reservations:** Ensure resources are available when needed. Often used with CUDs to guarantee capacity.

## 3. Instance Templates and Managed Instance Groups (MIGs)

- **Instance Templates:** Immutable resources that define VM properties (machine type, image, labels). Used to create MIGs.
- **Managed Instance Groups (MIGs):** A collection of identical VMs that offer high availability and scalability.
  - **Auto-healing:** Automatically recreates VMs that fail health checks.
  - **Auto-scaling:** Dynamically adds or removes VMs based on CPU utilization, load balancing capacity, or custom metrics.
  - **Regional MIGs:** Highly recommended for production as they distribute VMs across multiple zones in a region.

> - [Zonal MIG - Google Cloud Documentation](https://docs.cloud.google.com/compute/docs/instance-groups/create-zonal-mig)
> - [Regional MIG - Google Cloud Documentation](https://docs.cloud.google.com/compute/docs/instance-groups/distributing-instances-with-regional-instance-groups)

> **Live migration** is the process of moving a running VM from one physical host to another without downtime. Google uses this for infrastructure maintenance, allowing your VMs to keep running during host updates. It requires no action from you.

## 4. Persistent Disks, Snapshots and Images

- **Persistent Disks (PD):** Durable network storage. You can resize a disk up but never down.
- **Disk Types:**
  - **Standard PD:** HDD-based, cost-effective for sequential read/write workloads
  - **SSD PD:** Higher IOPS and throughput for demanding workloads (databases, apps)
  - **Hyperdisk:** Independent performance scaling (see [Section 4.1](./compute_engine.md#41-hyperdisk))
- **Disk Encryption:**
  - **Google-managed:** Default, encryption handled by Google
  - **Customer-Managed Keys (CMEK):** You control keys in Cloud KMS
  - **Customer-Supplied Keys (CSEK):** You provide and manage encryption keys
- **Snapshots:** Incremental backups of disks, stored globally. Best for disaster recovery.
- **Custom Images:** A _Gold Master_ boot disk with your OS and software pre-installed. Best for consistent deployments in MIGs.
- **Local SSD:** Physical drives attached directly to the host. Data is ephemeral and lost if the VM is stopped or deleted.
  > You can attach up to 24 local SSDs to a single VM, depending on the machine type. Each local SSD is 375 GB, providing up to 9 TB of local SSD storage per VM. Local SSDs provide high-performance ephemeral storage.

## 4.1. Hyperdisk

High-performance block storage with independent scaling of performance and capacity.

- **Hyperdisk Balanced**: SSD-like performance at lower cost. Good balance of price and performance.
- **Hyperdisk Extreme**: Ultra-high throughput and IOPS for demanding workloads (databases, AI/ML training, HPC).
- Performance scales independently from capacity (unlike standard Persistent Disks).
- Can be attached to sole-tenant nodes and used with MIGs.

<figure>
  <img src="images/compute_engine_summary.png" alt="Compute Engine Summary">
  <figcaption><center>Compute Engine Summary<br><i>Image source: Own work (Gemini Prompting)</i></center></figcaption>
</figure>

## 5. Sole-Tenant Nodes

Dedicated, single‑tenant physical servers in Google Cloud that run only your project’s Compute Engine VMs. They provide hardware‑level isolation by ensuring no other customer’s workloads share the same underlying host.

> [Sole-tenancy overview - Google Cloud Documentation](https://docs.cloud.google.com/compute/docs/nodes/sole-tenant-nodes)

**Primary Use Cases**

Regulatory or compliance requirements that mandate physical isolation (e.g., healthcare, finance, government).
Security boundaries where you must avoid multi‑tenant hardware for risk or policy reasons.
_Bring‑Your‑Own‑License (BYOL)_ scenarios for software that is licensed per physical core, socket, or host.
Workload placement control, such as pinning specific VMs to specific hardware types.

**Node Groups & Placement**

Nodes are organized into node groups, which act as pools of dedicated hosts.
VMs use **node affinity/anti‑affinity** rules to control placement, ensuring they land on the correct physical nodes.
You can enforce strict placement (must run on a specific node type) or preferred placement (try this node type first).
Useful for keeping related workloads together or separating sensitive workloads across different hosts.

<figure>
  <img src="images/compute_engine_sole_tenant.png" alt="Compute Engine Sole Tenant Summary">
  <figcaption><center>Compute Engine Sole Tenant Summary<br><i>Image source: Own work (Gemini Prompting)</i></center></figcaption>
</figure>

## 6. Connecting to Instances

- **SSH Access**: `gcloud compute ssh [VM_NAME]`
  - Uses a **direct SSH connection** to the VM’s **public IP**
  - Requires the VM to **have an external IP**
  - Firewall must allow TCP on port `22` from your client
  - Your machine connects **over the public internet**
- **Identity-Aware Proxy (IAP)**: `gcloud compute ssh VM_NAME --zone=ZONE --tunnel-through-iap`
  - Uses **IAP TCP Tunneling** (Zero‑Trust access)
  - Works even when the VM has **no external IP**
  - Requires IAM role: `roles/iap.tunnelResourceAccessor`
  - Firewall must allow TCP on port `22` from **IAP’s IP range `35.235.240.0/20`**
  - SSH traffic goes through Google’s secure IAP tunnel to the VM’s **internal IP**

## 7. Service Accounts and Metadata

- **Service Accounts**: VMs use these to authenticate to other Google Cloud services (GCS, BigQuery). Always use custom service accounts with _Least Privilege_ for production.
  > The default Compute Engine service account `PROJECT_NUMBER-compute@developer.gserviceaccount.com` is automatically created and has the Editor role on the project. It is automatically attached to new VMs unless you specify a different service account or disable it.
- **Service Account Scopes**: Control what APIs the service account can access
  - **Project-wide**: Applies to all VMs using the default service account
  - **Instance-level**: Set per-VM for granular control
- **Metadata**: Used to pass configuration data. Startup scripts are automated scripts that run every time the VM boots.
- **Metadata Server**: Accessible at `http://metadata.google.internal/computeMetadata/v1/`.

### 7.1. VM Security and Availability

- **Shielded VMs:** Hardened VMs with security features to protect against boot-level malware/rootkits
  - **Secure Boot:** Blocks untrusted boot loaders and drivers
  - **vTPM:** Virtual Trusted Platform Module for key storage and measurement
  - **Integrity Monitoring:** Verifies VM boot chain hasn't been compromised
- **Confidential Computing:** Encryption at runtime using AMD SEV-SNP. Protects data while it's being processed.
- **Availability Policies:**
  - **On-host maintenance:** Controls behavior during host maintenance (Migrate/Terminate)
  - **Automatic restart:** Whether GCP restarts VM after unexpected failure
  - **Provisioning model:** Standard vs Spot (affects pricing and preemptibility)
- **GPUs Available:** T4, A100, H100. Each has specific licensing requirements and zone availability.

## 8. Essential `gcloud` Commands

- **Create a VM**: `gcloud compute instances create [NAME] --zone=[ZONE] --machine-type=[TYPE]`
- **Resize a MIG**: `gcloud compute instance-groups managed resize [NAME] --size=[NEW_SIZE]`
- **List Instances**: `gcloud compute instances list`
- **Restart instances in a _rolling-action_** (e.g. use case → reclaim leaked memory):
  ```bash
  gcloud compute instance-groups managed rolling-action restart [MIG_NAME] \
    --max-unavailable=20% \
    --region=[REGION]
  ```
- **Replace instances _rolling-action_** (e.g. use case → update a start-up script (new template)):
  ```bash
  gcloud compute instance-groups managed rolling-action start-update [MIG_NAME] \
    --version=template=[TEMPLATE_NAME] \
    --max-unavailable=0 \
    --max-surge=1
    --zone=[ZONE]
  ```
  > For more details see [`gcloud compute instance-groups managed rolling-action start-update`](https://docs.cloud.google.com/sdk/gcloud/reference/compute/instance-groups/managed/rolling-action/start-update) Google Cloud Documentation.

### 8.1. Deletion Protection

Enabling **deletion protection** on a Google Cloud Compute Engine instance prevents accidental deletion of the VM. When this setting is active, any attempt to delete the instance (via the Console, gcloud, API, or Terraform) will fail with an error unless you explicitly disable the protection first.

Enabling:

```bash
gcloud compute instances update my-web-server \
  --region=us-central1 \
  --project=my-gcp-project \
  --deletion-protection
```

Disabling:

```bash
gcloud compute instances update my-web-server \
  --region=us-central1 \
  --project=my-gcp-project \
  --no-deletion-protection
```

For more details see [Prevent accidental VM deletion](https://docs.cloud.google.com/compute/docs/instances/preventing-accidental-vm-deletion).

### 8.2. First login to Windows VM via RDP

Generates or resets local admin credentials for Windows VMs. Essential for RDP access since Windows doesn't use SSH keys.

- _Injection_ → Sends a request to the Metadata Server; the Google Guest Agent inside the VM picks it up and updates the OS password locally.
- _Initial Login_ → Mandatory for first-time access to generic Windows images.
- _Recovery_ → Used to regain access if local credentials are lost.

```bash
gcloud compute reset-windows-password [VM_NAME] --user=[USER] --zone=[ZONE]
```

- _Permissions_ → Needs `compute.instances.setMetadata`.
- _Prerequisite_ → VM must be Running with _Guest Agent_ installed.
- _Scope_ → Only affects local accounts, not Active Directory (domain) accounts.

## 9. Exam Tips

- **Preemption:** If a Spot VM is terminated, it is a preemption, not a system crash.
- **Zonal vs. Regional MIG:** Choose Regional MIG for the highest availability.
- **Metadata Header:** Requests to the metadata server require the header `Metadata-Flavor: Google`.
- **Machine Type Selection:** If a question asks for the best cost-performance for a general workload, consider **E2** or **N4**. For high-performance databases, consider **C4** or **M3**.

## 10. External Links

- [Compute Engine - The Cloud Girl](https://www.thecloudgirl.dev/compute/compute-engine)
- [Where should I run my staff - The Cloud Girl](https://www.thecloudgirl.dev/compute/where-should-i-run-my-stuff)
