# Cloud Run: ACE Exam Study Guide (2026)

## 1. Cloud Run Overview

Cloud Run is a managed compute platform that enables you to run containerized applications that are stateless and request-aware. It is built on **Knative**, an open-source standard for serverless.

- **Key Characteristics:**
  - **Serverless:** No infrastructure to manage. It scales automatically based on incoming requests.
  - **Scale to Zero:** If there is no traffic, Cloud Run scales down to zero instances.
  - **Stateless:** Containers must be stateless. Persistent data should be stored in Cloud Storage, Filestore, or a database.
  - **Concurrency:** Cloud Run can handle multiple concurrent requests per container instance (up to 1000).
- **Gemini for Cloud Run (2026 Update):** Gemini is now integrated for real-time deployment assistance, container optimization suggestions, and log-based troubleshooting.

## 2. Deployment Methods

- **Deploy from Container Image:** Provide a URL to an image in **Artifact Registry**.
- **Deploy from Source:** Cloud Run uses **Cloud Build** and **Buildpacks** to automatically create an image and deploy it.
- **Sidecar Containers (2026 Update):** Support for multiple containers in a single pod (sidecars) for tasks like logging, monitoring, or acting as a local proxy.
- **Jobs vs. Services:**
  - **Cloud Run Services:** For code that handles requests (HTTP/gRPC).
  - **Cloud Run Jobs:** For code that performs work and exits when finished.

## 3. Revisions and Traffic Management

- **Revisions:** Every time you deploy a change, Cloud Run creates a new **immutable** revision.
- **Traffic Splitting:** Split traffic between multiple revisions for Canary testing.
- **Tagging:** Assign a specific URL to a revision using tags for testing before routing main traffic.
- **Rollbacks:** Instantly roll back to a previous revision by shifting 100% of traffic.

## 4. Scaling and Resources

- **Maximum Instances:** Limits how far the service can scale up (prevents runaway costs).
- **Minimum Instances (Warm Start):** Keeps instances running to avoid "cold start" latency.
- **CPU Allocation:** CPU can be allocated only during request processing or always allocated.
- **GPU Support (2026 Update):** Cloud Run now supports GPU acceleration for AI/ML inference and data processing workloads.

## 5. Networking and Ingress

- **Ingress Settings:** All (internet), Internal (VPC only), or Load Balancing only.
- **Direct VPC Egress (2026 Update):** A faster, more direct way to connect to a VPC without requiring a Serverless VPC Access Connector.
- **Static Outbound IP:** Route traffic through a VPC and use **Cloud NAT**.

## 6. Security and IAM

- **Invoker Role:** `roles/run.invoker` is required to call a service. Public access is granted via the `allUsers` principal.
- **Service Account:** Assign a **Custom Service Account** with minimal permissions.
- **Secrets:** Use **Secret Manager** to mount secrets as environment variables or volumes.

## 7. Storage

- **In-memory:** Ephemeral filesystem limited by RAM.
- **Cloud Storage FUSE:** Mount a Cloud Storage bucket as a local volume.
- **NFS (Filestore):** Use VPC egress to mount a Filestore instance for high-performance shared storage.

## 8. Essential gcloud Commands

- **Deploy from Image:** `gcloud run deploy [SERVICE_NAME] --image [IMAGE_URL]`
- **Deploy from Source:** `gcloud run deploy [SERVICE_NAME] --source .`
- **Update Traffic:** `gcloud run services update-traffic [SERVICE_NAME] --to-revisions [REV1=PERCENT,REV2=PERCENT]`

## 9. Exam Tips & Comparison

- **Cloud Run vs. GKE:** Use Cloud Run for simplicity and scale-to-zero. Use GKE for full Kubernetes control.
- **Gemini Usage:** Use Gemini to optimize your Dockerfile or to explain why a container failed to start.
- **Sidecars:** If an exam question asks for a way to run a logging agent alongside your app, the answer is **Sidecars**.
