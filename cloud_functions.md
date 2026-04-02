# Cloud Functions: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Cloud Functions Overview

Cloud Functions is a serverless, event-driven compute platform for executing snippets of code in response to events.

- **Key Characteristics:**
  - **Serverless:** No infrastructure management; automatic scaling.
  - **Single-purpose:** Best for small, independent units of logic.
  - **Ephemeral:** Instances are created, perform work, and are destroyed.
  - **Gemini for Code:** Use Gemini to generate boilerplate code, optimize logic, and write unit tests for your functions.
- **Generations (2nd Gen is now the Default):**
  - **2nd Generation (Built on Cloud Run):**
    - Higher concurrency (up to 1000 requests per instance).
    - Longer processing times (up to 60 minutes for HTTP).
    - Larger instance sizes (up to 16GB RAM / 4 vCPUs) and support for C4 machine types.
    - Traffic splitting between revisions.
  - **1st Generation:** Legacy model, limited concurrency (1 request per instance).

## 2. Triggers and Events

Cloud Functions run in response to specific events.

- **HTTP Triggers:** Triggered via a direct URL (standard for webhooks or simple APIs).
- **Event-Driven Triggers (via Eventarc):**
  - **Cloud Storage:** Triggered by file creation, deletion, or metadata updates.
  - **Pub/Sub:** Triggered when a message is published to a specific topic.
  - **Firestore:** Triggered by document creation, updates, or deletions.
  - **Cloud Logging:** Triggered by specific log entries (via Eventarc).

## 3. Runtimes and Deployment

- **Supported Languages:** Node.js, Python, Go, Java, Ruby, PHP, .NET Core.
- **Deployment Source:**
  - Local machine via `gcloud`.
  - Source repositories (GitHub, Bitbucket).
  - Cloud Storage (ZIP file).
- **Cloud Build:** When you deploy, Cloud Build automatically packages the function and stores it as a container image in *Artifact Registry*.

## 4. Scaling and Concurrency

- **Max Instances:** Limits scaling to prevent excessive costs.
- **Min Instances:** Keeps instances "warm" to eliminate cold start latency.
- **Concurrency (2nd Gen Only):** Allows a single instance to handle multiple simultaneous requests, reducing the total number of instances needed.
- **Timeout:** The maximum time a function can run before being terminated.

## 5. Networking

- **Ingress Settings:** Control whether the function is public or internal-only.
- **VPC Access:** Use a Serverless VPC Access Connector to allow the function to reach internal resources (e.g., Cloud SQL with private IP, Redis).
- **Static Outbound IP:** Requires a VPC Connector and Cloud NAT.

## 6. Security and IAM

- **Permissions:**
  - `roles/cloudfunctions.invoker`: Allows calling/triggering the function.
  - `roles/cloudfunctions.admin`: Full control over functions.
- **Service Accounts:**
  - **Runtime Service Account:** The identity the function uses when it runs (default is the App Engine default service account). Best practice: Use a *Custom Service Account* with minimal permissions.
- **Secrets:** Integrate with *Secret Manager* to securely provide API keys or credentials.

## 7. Monitoring and Logging

- **Cloud Logging:** All `stdout` and `stderr` output is automatically sent to Cloud Logging.
- **Error Reporting:** Automatically captures unhandled exceptions.
- **Cloud Monitoring:** Tracks execution counts, execution times, and memory usage.

## 8. Essential gcloud Commands

- **Deploy (HTTP):** `gcloud functions deploy [NAME] --gen2 --runtime [RUNTIME] --trigger-http --allow-unauthenticated`
- **Deploy (Pub/Sub):** `gcloud functions deploy [NAME] --gen2 --runtime [RUNTIME] --trigger-topic [TOPIC_NAME]`
- **List Functions:** `gcloud functions list`
- **Check Logs:** `gcloud functions logs read [NAME]`
- **Describe Function:** `gcloud functions describe [NAME]`

## 9. Exam Tips & Comparison

- **Cloud Functions vs. Cloud Run:** Use Functions for event-driven snippets or simple glue logic. Use Cloud Run for full web applications, containers with multiple routes, or complex dependencies.
- **Cold Starts:** Occur when a new instance is spun up from zero. Mitigated by setting a `min-instances` value.
- **Idempotency:** _Critical._ Event-driven functions should be idempotent (running the same event multiple times should produce the same result) to handle retries correctly.

[Back to README](README.md)
