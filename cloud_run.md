# GCP Cloud Run: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud Run Overview

Cloud Run is a managed compute platform that enables you to run containerized applications that are stateless and request-aware. It is built on **Knative**, an open-source standard for serverless.

- **Key Characteristics:**
  - **Serverless:** No infrastructure to manage. It scales automatically based on incoming requests.
  - **Scale to Zero:** If there is no traffic, Cloud Run scales down to zero instances (saving costs).
  - **Stateless:** Containers must be stateless. Persistent data should be stored in Cloud Storage, Filestore, or a database (Cloud SQL, Firestore).
  - **Concurrency:** Unlike Cloud Functions (which handle one request per instance), Cloud Run can handle multiple concurrent requests per container instance (up to 1000).

## 2. Deployment Methods

- **Deploy from Container Image:** Provide a URL to an image in **Artifact Registry** (preferred) or Container Registry.
- **Deploy from Source:** Cloud Run uses **Cloud Build** and **Buildpacks** to automatically create a container image from your source code and deploy it.
- **Jobs vs. Services:**
  - **Cloud Run Services:** For code that handles requests (HTTP). Standard for web apps and APIs.
  - **Cloud Run Jobs:** For code that performs work and exits when finished (e.g., database migrations, batch processing).

## 3. Revisions and Traffic Management

- **Revisions:** Every time you deploy a change, Cloud Run creates a new **immutable** revision.
- **Traffic Splitting:** You can split traffic between multiple revisions (e.g., 5% to a new version for Canary testing, 95% to the stable version).
- **Tagging:** You can assign a specific URL to a revision using tags for testing before routing main traffic to it.
- **Rollbacks:** You can instantly roll back to a previous revision by shifting 100% of traffic back to it.

## 4. Scaling and Resources

- **Maximum Instances:** Limits how far the service can scale up (prevents runaway costs).
- **Minimum Instances (Warm Start):** Keeps a minimum number of instances running even with no traffic to avoid "cold start" latency.
- **Concurrency:** The maximum number of simultaneous requests a single instance can handle.
- **CPU Allocation:**
  - **CPU allocated only during request processing:** Default. Cheapest.
  - **CPU always allocated:** Useful for background tasks that need to run outside of active requests.

## 5. Networking and Ingress

- **Ingress Settings:**
  - **All:** Accessible from the internet.
  - **Internal:** Accessible only from within your VPC or other Google Cloud services.
  - **Internal and Cloud Load Balancing:** Accessible only via an external HTTP(S) Load Balancer.
- **VPC Access:** Use a **Serverless VPC Access Connector** to allow Cloud Run to access resources with internal IPs (e.g., a Cloud SQL instance without a public IP).
- **Static Outbound IP:** To give Cloud Run a static IP for external firewalls, route its traffic through a VPC Connector and use **Cloud NAT**.

## 6. Security and IAM

- **Invoker Role:**
  - `roles/run.invoker`: Required to call a service.
  - **Public Access:** Grant the `allUsers` principal the `roles/run.invoker` role.
- **Service Account:** By default, Cloud Run uses the Default Compute Service Account. For better security, assign a **Custom Service Account** with minimal permissions.
- **Secrets:** Never hardcode secrets. Integrate with **Secret Manager** to mount secrets as environment variables or volumes.

## 7. Storage

- **In-memory:** Filesystem is writable but ephemeral and limited by available RAM.
- **Cloud Storage FUSE:** Mount a Cloud Storage bucket as a local volume for easy file access.
- **NFS (Filestore):** Use a VPC Connector to mount a Filestore instance for high-performance shared storage.

## 8. Essential gcloud Commands

- **Deploy from Image:** `gcloud run deploy [SERVICE_NAME] --image [IMAGE_URL]`
- **Deploy from Source:** `gcloud run deploy [SERVICE_NAME] --source .`
- **Update Traffic:** `gcloud run services update-traffic [SERVICE_NAME] --to-revisions [REV1=PERCENT,REV2=PERCENT]`
- **List Revisions:** `gcloud run revisions list --service [SERVICE_NAME]`
- **Create a Job:** `gcloud run jobs create [JOB_NAME] --image [IMAGE_URL]`
- **Execute a Job:** `gcloud run jobs execute [JOB_NAME]`

## 9. Exam Tips & Comparison

- **Cloud Run vs. GKE:** Use Cloud Run for simplicity and scale-to-zero. Use GKE for full Kubernetes control, specialized networking, or non-HTTP protocols.
- **Cloud Run vs. Cloud Run Functions:** Use Cloud Run for complex apps with multiple routes or high concurrency. Use Functions for simple, event-driven snippets.
- **HTTP/2 and gRPC:** Cloud Run supports both, along with WebSockets.

[Back to root](./README.md)
