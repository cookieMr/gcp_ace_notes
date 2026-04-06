# App Engine: ACE Exam Study Guide (2026)

![App Engine](images/app_engine.png)

_Image source: Google Cloud Documentation_

## 1. App Engine Overview

App Engine is a fully managed _Platform as a Service (PaaS)_ for building and deploying web applications and APIs.

- Key Characteristics
  - **Serverless:** No server management; automatic scaling.
  - **Application-Centric:** Focus on code, not infrastructure.
  - **Regional Resource:** An App Engine application is created within a specific region and cannot be moved once created.
  - **Max One App per Project:** You can only have one App Engine application per Google Cloud project.

## 2. Standard vs. Flexible Environment

> This is the most frequent exam topic for App Engine.

### Standard Environment

- **Speed:** Starts in seconds. Scale-to-zero is supported.
- **Infrastructure:** Runs in sandboxed environments (specific versions of Node.js, Python, Java, etc.).
- **Constraints:** You cannot modify the underlying OS or write to the local filesystem (except `/tmp`). No SSH access.
- **Cost:** Generally cheaper for apps with intermittent traffic due to rapid scaling and scale-to-zero.
- **Best For:** Standard web apps, high-scale APIs with varying traffic.

### Flexible Environment

- **Speed:** Starts in minutes (uses _Compute Engine VMs_). No scale-to-zero.
- **Infrastructure:** Runs your code in Docker containers. You can use any language or version.
- **Machine Types:** Supports modern N4 and C4 machine types for high-performance workloads.
- **Capabilities:** You can modify the OS, access the local filesystem, and use SSH.
- **Connectivity:** Can access resources in your VPC more easily than Standard.
- **Cost:** More expensive; at least one instance must always be running.
- **Best For:** Apps with consistent traffic, apps requiring custom libraries/OS-level dependencies, or those needing high CPU/Memory.

## 3. App Engine Hierarchy

Understanding the relationship between components is essential for resource management.

1.  **Project:** The root Google Cloud resource.
2.  **Application:** The App Engine app within the project (one per project).
3.  **Service:** Microservices within the app (e.g., "frontend", "api", "worker").
4.  **Version:** Different versions of a service (e.g., "v1", "v2").
5.  **Instance:** The actual running units of a version.

## 4. Scaling Types

- **Automatic Scaling:** Based on CPU, throughput, or latency. Supported by both _Standard_ and _Flexible_.
  - Only Standard can scale to zero.
- **Basic Scaling:** Only for _Standard_. Instances are created when a request is received and turned off when idle.
- **Manual Scaling:** You specify the exact number of instances. Supported by both.

## 5. Traffic Management

- **Traffic Migration** - Gradually shifts all traffic from one version to another. Useful for controlled rollouts, such as moving traffic from `v1` to `v2` without an abrupt cutover.
- **Traffic Splitting** - Routes live traffic to multiple versions at the same time. Common use cases include _A/B testing_ (e.g., 50/50 split), _Canary releases_ (e.g., 1% to a new version), and progressive rollouts with real user traffic.
- **Methods** - App Engine can distribute traffic using:
  - _IP-based_ splitting — consistent routing for users behind the same IP.
    ```bash
    gcloud app services set-traffic my-service \
      --splits v1=0.9,v2=0.1 \
      --split-by ip
    ```
  - _Cookie-based_ splitting — sticky sessions (per user) for experiments or A/B tests. App Engine uses the `GOOGAPPUID` cookie.
    ```bash
    gcloud app services set-traffic my-service \
      --splits v1=0.5,v2=0.5 \
      --split-by cookie
    ```
  - _Random_ splitting — evenly distributed, non-sticky traffic.
    ```bash
    gcloud app services set-traffic my-service \
      --splits v1=0.99,v2=0.01 \
      --split-by random
    ```

## 6. Deployment and Configuration

- **`app.yaml`**: The core configuration file used for deployment. Defines the runtime, scaling, environment variables, and handlers.

  _App Engine Flexible (Java) - example `app.yaml` file_
  ```yaml
  runtime: java
  env: flex

  runtime_config:
    jdk: openjdk21

  automatic_scaling:
    min_num_instances: 1
    max_num_instances: 5

  resources:
    cpu: 1
    memory_gb: 2
    disk_size_gb: 10
  ```

- **Deployment**: Use `gcloud app deploy`. By default, this promotes the new version to handle 100% of traffic. Use `--no-promote` to deploy without switching traffic.

## 7. Networking and Security

- **App Engine Firewall:** Control access by IP range (Allow or Deny).
- **IAP (Identity-Aware Proxy):** Use to restrict access to the app based on IAM identities (users/groups) without modifying application code.
- **VPC Access:** Use a **Serverless VPC Access Connector** (mostly for Standard) to allow the app to reach resources with private IPs (e.g., Cloud SQL, Memorystore).
- **Service Accounts:** App Engine uses the **App Engine Default Service Account** (`project-id@appspot.gserviceaccount.com`) by default. It has broad "Editor" permissions; best practice is to use a custom service account.

## 8. Essential gcloud Commands

- **Initialize App:** `gcloud app create --region [REGION]`
- **Deploy App:** `gcloud app deploy [YAML_FILE]`
- **Deploy without traffic shift:** `gcloud app deploy --no-promote`
- **Split Traffic:** `gcloud app services set-traffic [SERVICE] --splits [V1=0.5,V2=0.5]`
- **Browse App:** `gcloud app browse`
- **Check Logs:** `gcloud app logs read`

## 9. Exam Tips & Comparison

- **App Engine vs. Cloud Run:** Use App Engine for traditional, stateful-ish web apps or when you want the simplest "push code" experience. Use Cloud Run for modern containerized microservices that need to scale to zero.
- **Versioning:** Always deploy a new version for major changes. This allows for instant rollbacks if things go wrong.
- **Region Lock:** You cannot change an App Engine app's region after it is created. You must create a new project to change the region.

## 10. External Links

- [App Engine - Google Cloud Documentation](https://docs.cloud.google.com/appengine/docs)
- [Youtube - Andrew Brown - App Engine](https://www.youtube.com/watch?v=OlAmyf8_4O4&t=10827s)
