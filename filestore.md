# Filestore: ACE Exam Study Guide (2026)

[Back to README](README.md)

Google Cloud Filestore is a **managed NFS file storage service** designed for applications that require a **POSIX‑compliant shared filesystem**.  
It is commonly used with **GKE**, **Compute Engine**, and workloads that need shared file access.

---

## 1. What Filestore Is

Filestore is:

- A **managed NFSv3** file server
- Provides **shared file storage** accessible by multiple VMs or containers
- Designed for **low‑latency**, **high‑throughput** workloads
- Regional service (data stored in a single region)

Filestore is **not** object storage (Cloud Storage) and **not** block storage (Persistent Disk).

---

## 2. Filestore Use Cases

Common use cases:

- Shared storage for **GKE** workloads
- Shared filesystem for **Compute Engine** instances
- Content management systems
- Media processing pipelines
- Machine learning workloads needing shared datasets
- Home directories for Linux users

---

## 3. Filestore Tiers (Updated for 2026)

Filestore tiers determine performance, availability, and capacity limits. **Note:** You cannot change tiers in-place; you must migrate data to a new instance.

| Tier           | Availability        | Capacity           | Use Case                                           |
| :------------- | :------------------ | :----------------- | :------------------------------------------------- |
| **Basic HDD**  | Single Zone         | 1 TiB – 63.9 TiB   | Low-cost, sequential workloads, dev/test.          |
| **Basic SSD**  | Single Zone         | 2.5 TiB – 63.9 TiB | General purpose, legacy apps, read-heavy.          |
| **Zonal**      | Single Zone (99.9%) | 1 TiB – 100 TiB    | HPC, AI/ML, High throughput (formerly High Scale). |
| **Regional**   | Multi-Zone (99.99%) | 100 GiB – 100 TiB  | Mission-critical apps, DR-ready.                   |
| **Enterprise** | Multi-Zone (99.99%) | 1 TiB – 10 TiB     | **GKE Multishares**, high availability, NFSv4.1.   |

- **ACE Tip:** Choose **Zonal** for performance, **Regional/Enterprise** for HA, and **Basic** for cost-sensitive dev workloads.

---

## 4. Networking & Connectivity

- Deployed into a **VPC network** via a private IP.
- Must be in the **same VPC and region** as clients (or connected via VPC Peering/VPN).
- **Serverless Support (Critical):** Cloud Run (Gen 2) **can** mount Filestore using **Direct VPC Egress**.
- **Service Networking API:** Filestore uses VPC Peering internally to connect to your network.

Clients can be:

- Compute Engine VMs
- GKE nodes
- Cloud Run (Gen 2)
- Bare-metal hybrid via VPN/Interconnect

---

## 5. Mounting Filestore

### On Compute Engine / GKE:

Standard NFS mounting applies. Use the **Filestore CSI driver** for GKE to provision volumes automatically.

### On Cloud Run (Gen 2):

Use the `--add-volume` flag with type `nfs`.

```bash
gcloud run services update [SERVICE] \
  --add-volume=name=fs-vol,type=nfs,location=[IP]:/[SHARE] \
  --add-volume-mount=volume=fs-vol,mount-path=/mnt/data \
  --vpc-egress=all-traffic --network=[VPC] --subnet=[SUBNET]
```

---

## 6. Capacity & Scaling

- Minimum capacity depends on tier
- Basic tiers scale **capacity and performance together**
- High Scale SSD scales **independently**
- You can **increase** capacity but **cannot decrease** it
- Scaling may cause **brief downtime** (except Enterprise tier)

---

## 7. Security

Filestore security includes:

- VPC‑level isolation
- IAM for **instance management**, not file access
- NFS-level permissions (POSIX)
- CMEK support for some tiers (Enterprise)

Important:  
IAM **does not** control file‑level access — NFS permissions do.

---

## 8. Monitoring & Logging

Filestore integrates with:

- **Cloud Monitoring** (IOPS, throughput, capacity usage)
- **Cloud Logging** (audit logs)

You should know how to troubleshoot:

- High latency
- Running out of capacity
- NFS mount failures

---

## 9. Using in a Spring Boot App (Example)

Filestore is mounted as a local directory. In Spring Boot, you simply use the standard `java.nio.file` API to read and write files.

```java
@Service
public class FileService {
    private final Path mountPoint = Paths.get("/mnt/filestore/data");

    public void saveFile(String fileName, byte[] content) throws IOException {
        Files.write(mountPoint.resolve(fileName), content);
    }

    public byte[] readFile(String fileName) throws IOException {
        return Files.readAllBytes(mountPoint.resolve(fileName));
    }
}
```

---

## 10. Common ACE Exam Scenarios

- **Scenario: Shared POSIX for GKE?** → Filestore.
- **Scenario: Many small (10GB) shares for GKE pods?** → Filestore **Enterprise (Multishares)**.
- **Scenario: Mount shared storage to Cloud Run?** → Filestore + **Direct VPC Egress**.
- **Scenario: Scale performance and capacity independently?** → **Zonal** tier.
- **Scenario: In-place tier upgrade?** → **Not possible** (must create new and migrate).
- **Scenario: Regional High Availability (99.99% SLA)?** → **Regional** or **Enterprise** tier.
- **Scenario: Global object storage?** → **Cloud Storage** (not Filestore).

---

## 11. Quick Summary Table

| Feature           | Filestore           | Cloud Storage         | Persistent Disk       |
| :---------------- | :------------------ | :-------------------- | :-------------------- |
| **Protocol**      | NFSv3 / NFSv4.1     | HTTP(S) / API         | Block (SCSI/NVMe)     |
| **Shared Access** | ReadWriteMany (RWX) | ReadWriteMany (RWX)   | ReadWriteOnce (RWO)\* |
| **POSIX**         | Full                | Partial (via GCSFuse) | Full                  |
| **Cloud Run**     | ✔️ (via Gen2)       | ✔️                    | ❌                    |
| **HA**            | Regional Tier       | Regional/Multi-Reg    | Regional PD           |

_\*Note: Multi-writer PD exists but is highly specialized (Block storage)._

---

## Final ACE Tips

- Filestore = **managed NFS** (POSIX compliant).
- **Zonal** = High Performance (formerly High Scale).
- **Regional/Enterprise** = High Availability (99.99% SLA).
- Cloud Run (Gen 2) **can** mount Filestore via Direct VPC Egress.
- **Multishares** (Enterprise) allow many small shares on one instance.
- **No in-place tier upgrades.**
- IAM = Instance management; NFS/POSIX = File-level access.

[Back to README](README.md)
