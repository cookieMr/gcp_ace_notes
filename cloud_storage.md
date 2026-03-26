# Google Cloud Storage — ACE Exam Study Guide

[Back to root](./README.md)

## 1. What Cloud Storage Is

Cloud Storage is Google’s **object storage** service for storing unstructured data such as:

- files
- images
- backups
- logs
- datasets
- static website assets

It is:

- **global**
- **highly durable (11 nines)**
- **infinitely scalable**
- **serverless**

### **Strong Global Consistency**

- GCS provides **strong global consistency** for all operations
  (Read-after-write, Read-after-update, Read-after-delete, and List).
- If you upload or delete an object, a subsequent "list" or "get"
  will immediately reflect that change globally.

---

## 2. Buckets (Core Concept)

Buckets are:

- **global namespace** (bucket names must be globally unique across all GCP)
- **regional or multi‑regional**
- **Naming Rules:** 3–63 chars, lowercase letters, numbers, hyphens,
  and underscores. **No dots** unless it's a verified domain.
  No sensitive info (PII) in names.

Bucket properties:

- Location (region, dual‑region, multi‑region)
- Storage class
- Access control (IAM or ACL)
- Encryption settings
- Lifecycle rules
- Versioning

---

## 3. Bucket Locations

### **Regional**

- Stored in a single region
- Lowest latency for compute in same region
- Cheapest option

### **Dual‑Region**

- Stored in two specific regions
- High availability
- Good for DR

### **Multi‑Region**

- Stored across large geographic areas
- Best for global content distribution

---

## 4. Storage Classes

| Storage Class | Use Case                       | Availability | Min Duration | Retrieval Fee |
| ------------- | ------------------------------ | ------------ | ------------ | ------------- |
| **Standard**  | Hot data, frequent access      | >99.9%       | None         | None          |
| **Nearline**  | Access ~once per month         | 99.0%        | 30 days      | Low           |
| **Coldline**  | Access ~once per quarter       | 99.0%        | 90 days      | Higher        |
| **Archive**   | Rare access, long‑term storage | 99.0%        | 365 days     | Highest       |

All classes share: **same durability (11 nines)** and **same low latency** (time to first byte).
_Exam Tip:_ Moving data to a colder class is free, but retrieving it costs money (except for Standard).

Only cost and minimum storage duration differ.

---

## 5. Access Control

Two models:

### **IAM (recommended)**

- Controls access at bucket or project level
- Easy to manage
- Supports uniform bucket‑level access

### **ACLs (legacy)**

- Object‑level permissions
- More granular but harder to manage
- Disabled when uniform access is enabled

#### Uniform vs Fine‑Grained Access

- **Uniform** → IAM only
- **Fine‑grained** → IAM + ACLs

ACE exam often asks which to choose → **Uniform** is recommended.

#### **Public Access Prevention (PAP)**

- A bucket-level setting that prevents making objects public,
  even if IAM permissions or ACLs would otherwise allow it.
  Use this as a safety "kill-switch" for sensitive data.

---

## 6. Signed URLs & Signed Policy Documents

### **Signed URLs**

- Temporary access to a specific object
- No need for user to have IAM permissions
- Common for downloads/uploads

### **Signed Policy Documents**

- Allow controlled browser‑based uploads

---

## 7. Object Versioning & Lifecycle Management

### **Object Versioning**

- Keeps old versions of objects
- Useful for recovery
- Increases storage cost

### **Lifecycle Rules**

Automate actions such as:

- Move to cheaper storage class
- Delete old versions
- Delete objects after X days

ACE exam loves lifecycle questions.

---

## 8. Encryption

Cloud Storage supports:

### **Google‑managed keys (default)**

- Automatic
- No configuration needed

### **Customer‑managed keys (CMEK)**

- Stored in Cloud KMS
- You control rotation
- _Exam Tip:_ For CMEK, the KMS key **must be located in the same region** as the bucket.

### **Customer‑supplied keys (CSEK)**

- You provide the key with each request
- Rarely used

---

## 8a. Retention Policies & Bucket Lock

- **Retention Policy:** Ensures objects are not deleted/overwritten for a specific duration.
- **Bucket Lock:** Once locked, the policy cannot be removed or duration shortened.
- **Object Holds:** Prevents deletion of a specific object.
  - **Temporary Hold:** Placed/removed by users (e.g., for a legal case).
  - **Event-based Hold:** Remains until a specific event occurs (e.g., a contract ends).

---

## 9. Performance & Features

### **Parallel composite uploads**

- Speeds up large uploads

### **Requester Pays**

- Requester pays for access costs
- Useful for public datasets

### **Static Website Hosting**

- Buckets can serve static websites
- Requires public access + `index.html`

### **Cloud Storage FUSE**

- Allows you to mount a GCS bucket as a local file system on a Linux/macOS instance.
- **Warning:** It is not POSIX-compliant. Do not use it for high-performance databases.

### **CORS (Cross-Origin Resource Sharing)**

- Required if a website on a different domain needs to access assets
  (like fonts or scripts) stored in your bucket.

---

## 10. Interoperability

Cloud Storage supports:

- **S3‑compatible XML API**
- **JSON API**
- **gsutil**
- **Client libraries**

---

## 11. Tools You Must Know

### **gsutil** (Legacy)

Common commands:

```bash
gsutil mb gs://my-bucket
gsutil -m cp *.jpg gs://my-bucket   # -m for parallel/faster upload
gsutil mv gs://bucket1/file gs://bucket2/
gsutil rm -r gs://bucket/folder/    # -r for recursive delete
gsutil ls -r gs://bucket/**
```

### **gcloud storage** (The Modern CLI)

- Google's new, faster alternative to `gsutil`.
- Commands are nearly identical: `gcloud storage cp local.txt gs://bucket`.
- Expect to see both on the exam, but `gcloud storage` is the current recommendation.

### **Storage Transfer Service**

- Move data from AWS S3, Azure, or another GCS bucket

### **Transfer Appliance**

- Physical device for massive data migration

---

## 12. Using in a Spring Boot App (Example)

Add the dependency: `com.google.cloud:spring-cloud-gcp-starter-storage`.

```java
@Service
public class StorageService {
    @Value("gs://my-bucket/file.txt")
    private Resource gcsFile;

    public String readFile() throws IOException {
        return StreamUtils.copyToString(
            gcsFile.getInputStream(), Charset.defaultCharset());
    }

    public void writeFile(String content) throws IOException {
        try (OutputStream os = ((WritableResource) gcsFile).getOutputStream()) {
            os.write(content.getBytes());
        }
    }
}
```

---

## 13. Common ACE Exam Scenarios

### Scenario 1

You need to store frequently accessed data with low latency.  
→ **Standard Storage, Regional bucket**

### Scenario 2

You need to store backups accessed once per quarter.  
→ **Coldline**

### Scenario 3

You need to give temporary access to a file.  
→ **Signed URL**

### Scenario 4

You need to automatically delete logs older than 30 days.  
→ **Lifecycle rule**

### Scenario 5

You need object‑level permissions.  
→ **Fine‑grained access (ACLs)**

### Scenario 6

You need to prevent accidental deletion.  
→ **Object versioning**

### Scenario 7

You need to migrate data from AWS S3.  
→ **Storage Transfer Service**

### Scenario 8

You need to ensure that specific legal documents are not deleted until
a court case is closed, regardless of the bucket's lifecycle policy.
→ **Object Hold (Temporary Hold)**

---

## 13. Quick Summary Table

| Topic            | Key Points                            |
| ---------------- | ------------------------------------- |
| Storage classes  | Standard, Nearline, Coldline, Archive |
| Access control   | IAM (recommended), ACLs (legacy)      |
| Encryption       | Google‑managed, CMEK, CSEK            |
| Lifecycle        | Automate deletion/migration           |
| Versioning       | Keeps old object versions             |
| Signed URLs      | Temporary access                      |
| Bucket locations | Regional, Dual‑Region, Multi‑Region   |
| Tools            | gsutil, Storage Transfer Service      |

---

## Final ACE Tips

- Cloud Storage is **global**, but bucket locations are **regional**.
- Storage classes differ mainly in **cost** and **minimum storage duration**.
- Use **Signed URLs** for temporary access.
- Use **Lifecycle rules** to automate cost optimization.
- Use **CMEK** when compliance requires key control.
- **Uniform access** is recommended over ACLs.
- **gsutil** commands appear frequently in questions.

## Youtube Videos

For your reference while watching the videos:

- [Video 1](https://www.youtube.com/watch?v=OlAmyf8_4O4&t=9660):
  Watch from 2:41:00 to 3:00:15.
- [Video 2](https://www.youtube.com/watch?v=u43gJJrVa1I&t=49941):
  Watch from 13:10:32 to 13:35:00 for the deep dive into storage solutions.

[Back to root](./README.md)
