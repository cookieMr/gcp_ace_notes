# Persistent Disk: ACE Exam Study Guide

![Persistent Disk](images/persistent_disk.png)

_Image source: Google Cloud Documentation_

## 1. Overview

Persistent Disk is a durable storage solution for Google Cloud VMs. Data is replicated automatically for durability and resides independently from VM lifecycle.

**Key Characteristics:**

- Block storage (like a physical hard drive)
- Automatically encrypted by default (AES-256)
- Can be attached to only one VM at a time (except hyperdisk multi-writer mode)
- Survives VM termination/deletion
- Regional PD replicates data across zones automatically

## 2. Disk Types

### Standard Hard Disks

| Type                       | Use Case                                               | Performance           |
| -------------------------- | ------------------------------------------------------ | --------------------- |
| **Standard (pd-standard)** | Bulk storage, sequential reads (logs, data warehouses) | HDD-based, lower cost |

### SSD Hard Disks

| Type                       | Use Case                   | Performance                            |
| -------------------------- | -------------------------- | -------------------------------------- |
| **Balanced (pd-balanced)** | General purpose workloads  | SSD-based, balance of cost/performance |
| **SSD (pd-ssd)**           | Databases, high IOPS needs | High IOPS, consistent performance      |

### Extreme (Extreme Persistent Disk)

| Type                     | Use Case                                 | Performance                                      |
| ------------------------ | ---------------------------------------- | ------------------------------------------------ |
| **Extreme (pd-extreme)** | Maximum sustained IOPS, provisioned IOPS | Top-tier performance, explicit IOPS provisioning |

## 3. Disk Performance

| Metric                | pd-standard  | pd-balanced    | pd-ssd         | pd-extreme       |
| --------------------- | ------------ | -------------- | -------------- | ---------------- |
| Max IOPS/volume       | 1,800-6,000  | 6,000-30,000   | 15,000-100,000 | Up to 400,000    |
| Max Throughput/volume | 120-400 MB/s | 240-1,200 MB/s | 400-1,200 MB/s | Up to 6,000 MB/s |
| Cost                  | Lowest       | Moderate       | Higher         | Highest          |

**Factors affecting performance:**

- Disk size (larger disks = better baseline performance)
- Instance machine type (instance must support high IOPS)
- Number of vCPUs on the instance

## 4. Disk Size Limits

| Disk Type | Min Size | Max Size            |
| --------- | -------- | ------------------- |
| All types | 10 GB    | 257 TB (263,168 GB) |

**Important:** You can only increase disk size, not decrease it.

## 5. Local Solid-State Drive (Local SSD)

Local SSDs are physically attached to the host server, not the network.

**Characteristics:**

- **Temporary:** Data is lost when VM stops or is preempted
- **Highest performance:** Slower latency, higher IOPS than Persistent Disk
- **Use case:** Scratch space, caches, temporary data
- **Cost:** Charged while VM is running (not when stopped)
- **Encryption:** Always encrypted; keys managed by Google
- **Limit:** Maximum 8 Local SSD partitions (375 GB each = 3 TB total)

**Not for:** Databases, anything requiring durability

## 6. Regional Persistent Disk (High Availability)

Regional PD replicates data across two zones in the same region automatically.

**Use when:**

- High availability is required
- Cannot tolerate zone failure
- Running production workloads

**Trade-offs:**

- ~2x cost of zonal PD
- Higher write latency (data written to two zones)
- Cannot be used for disk sharing between instances

## 7. Snapshots

Snapshots are incremental backups of Persistent Disks stored in Cloud Storage.

**Characteristics:**

- **Incremental:** Only changes since last snapshot are stored (reduces cost)
- **Cross-region:** Can be used to create disks in different regions
- **Encryption:** Encrypted by default (Google-managed keys, or CMEK if configured)
- **Consistency:** For consistent snapshots of multiple disks, use `snapshot-schedule` with application-consistent quiescing

**Creating a snapshot:**

```
gcloud compute disks snapshot [DISK_NAME] --region=[REGION]
```

**Restoring from snapshot:**

```
gcloud compute disks create [NEW_DISK] --source-snapshot=[SNAPSHOT]
```

---

## 8. Disk Operations

### Attaching/Detaching

| Operation      | Command                                                         |
| -------------- | --------------------------------------------------------------- |
| Attach to VM   | `gcloud compute instances attach-disk [INSTANCE] --disk=[DISK]` |
| Detach from VM | `gcloud compute instances detach-disk [INSTANCE] --disk=[DISK]` |

**Rules:**

- Disk must be in same zone as VM
- Can attach while VM is running (hot-add)
- Must unmount filesystem before detaching

### Resizing

```
gcloud compute disks resize [DISK] --size=[NEW_SIZE_GB]
```

- **Always possible:** Increase disk size online (no restart needed for most OS)
- **Never possible:** Decrease disk size (must recreate disk at smaller size)
- After resizing: Must extend filesystem within the VM (`resize2fs`, `diskpart`, etc.)

### Moving Disks Between Zones

```
gcloud compute disks move [DISK] --destination-zone=[ZONE] --zone=[CURRENT_ZONE]
```

## 9. Sharing Disks

### Read-only Sharing

- Attach a single Persistent Disk to multiple VMs in read-only mode
- Use case: Sharing OS images, read-only data

### Multi-writer Mode (Hyperdisk)

- Allows attaching a disk to multiple VMs in read-write mode
- Requires hyperdisk type (Extreme, Throughput, or Balanced)
- Use case: Clustered databases (GCS, GlusterFS, etc.)

## 10. Encryption Options

| Option                   | Key Management   | Notes                                             |
| ------------------------ | ---------------- | ------------------------------------------------- |
| Google-managed           | Google           | Default, no configuration needed                  |
| Customer-managed (CMEK)  | Cloud KMS        | You control the keys, disk deleted if key deleted |
| Customer-supplied (CSEK) | You provide keys | Deprecated for most uses                          |

## 11. GKE Integration

Google Kubernetes Engine uses Persistent Disk primarily through _Kubernetes_ `PersistentVolumes` (PV) and `PersistentVolumeClaims` (PVC).

### Storage Classes

GKE uses predefined Storage Classes to provision Persistent Disks:

| Storage Class | Disk Type   | Use Case                     |
| ------------- | ----------- | ---------------------------- |
| `standard`    | pd-standard | Bulk storage, cost-effective |
| `balanced`    | pd-balanced | General purpose workloads    |
| `ssd`         | pd-ssd      | High-performance databases   |
| `extreme`     | pd-extreme  | Maximum IOPS workloads       |

### Volume Modes

- **Filesystem (default):** Mount as directory; supports `ReadWriteOnce` and `ReadOnlyMany`
- **Block:** Raw block device; supports `ReadWriteOnce` and `ReadWriteMany` (hyperdisk only)

### Access Modes

| Mode            | Description                                         |
| --------------- | --------------------------------------------------- |
| `ReadWriteOnce` | Single node read-write (most common)                |
| `ReadOnlyMany`  | Multiple nodes read-only                            |
| `ReadWriteMany` | Multiple nodes read-write (requires hyperdisk only) |

### StatefulSets

Use Persistent Disk with **StatefulSets** for workloads requiring stable identity and persistent storage:

- Each pod gets a unique PersistentVolumeClaim
- Pods are ordered for deployment/deletion
- Volume persists across pod rescheduling

### Key Points for Exam

- **Zonal:** GKE nodes and PD must be in the same zone
- **Regional clusters:** Use Regional PD for HA across zones
- **Node affinity:** PD auto-attaches to the node where the pod is scheduled
- **Disk size:** Cannot decrease PVC size (same as standalone PD)
- **Regional PD:** Requires GKE 1.26+ or GKE Standard mode for multi-zone volume placement

### Kubernetes config files

PersistentVolume (PV):

```bash
gcloud compute disks create my-gke-disk \
    --size=20Gi --zone=europe-central2-a
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: "my-pv"
  gcePersistentDisk:
    pdName: my-gke-disk
    fsType: ext4
```

PVC using default GKE _StorageClass_:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: "my-pv"
  volumeName: my-pv
```

Pod mounting the Persistent Disk:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: "/data"
          name: my-storage
  volumes:
    - name: my-storage
      persistentVolumeClaim:
        claimName: my-pvc
```

## 12. Common Exam Gotchas

1. **Data loss on stop:** Local SSD data is lost when VM stops (not Persistent Disk)
2. **Cannot decrease size:** You can only increase disk size
3. **Zone requirement:** Disk and VM must be in same zone
4. **Single attachment:** Standard PD can only attach to one VM at a time
5. **Snapshot deletion:** Deleting a snapshot does not delete the disk (incremental)
6. **Disk performance scales with size:** Larger disks = better IOPS/throughput
7. **Instance vCPU limits:** Instance must have enough vCPUs to utilize disk performance
8. **Boot disk deletion:** By default, boot disk is deleted when VM is deleted (check "Delete boot disk" checkbox to keep)

## 13. Practice Questions

**Q1:** You need storage that survives VM deletion. What should you use?

> **Answer:** Persistent Disk (local SSD is ephemeral)

**Q2:** A VM needs maximum IOPS for a database. What disk type?

> **Answer:** `pd-extreme` (or `pd-ssd` if not extreme needed)

**Q3:** A disk needs to be attached to multiple VMs simultaneously. What mode/type?

> **Answer:** Hyperdisk with multi-writer mode

## 14. Quick Reference Summary

| Feature              | Value                               |
| -------------------- | ----------------------------------- |
| Minimum size         | 10 GB                               |
| Maximum size         | 257 TB                              |
| Default encryption   | Google-managed (AES-256)            |
| Disk attachment      | One VM at a time (except hyperdisk) |
| Local SSD max        | 8 partitions x 375 GB = 3 TB        |
| Local SSD durability | Ephemeral (lost on stop/preempt)    |
| Regional PD zones    | Two zones in same region            |
| Snapshot type        | Incremental                         |
| Size change          | Increase only                       |

## 15. External Links

- [Persistent Disk - The Cloud Girl](https://www.thecloudgirl.dev/storage2/persistent-disk)
- [Which Storage Should I Use - The Cloud Girl](https://www.thecloudgirl.dev/storage2/which-storage-should-i-use)
- [What are different storage types - The Cloud Girl](https://www.thecloudgirl.dev/storage2/types-of-storage)
