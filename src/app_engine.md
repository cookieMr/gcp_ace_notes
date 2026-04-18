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
- **Infrastructure:** Runs in sandboxed environments (specific versions of Node.js, Python, Java, Go, PHP, Ruby).
- **Instance Classes:** M1 (high memory), M2 (high CPU), F1-F4 (default). Determines CPU/memory ratio.
  | Class | Memory | CPU | Cost |
  |-------|--------|-----|------|
  | F1 | 256MB | 600MHz | Cheapest |
  | F2 | 512MB | 1.2GHz | |
  | F4 | 1GB | 2.4GHz | |
  | M1 | 1GB | 600MHz | High memory |
  | M2 | 2GB | 1.2GHz | High memory |
- **Constraints:** Cannot modify OS; write-only to local filesystem (`/tmp`). No SSH access.
- **Cost:** Cheaper for intermittent traffic; scale-to-zero saves money.
- **Best For:** Web apps, APIs with varying traffic, rapid development.

### Flexible Environment

- **Speed:** Starts in minutes (uses Compute Engine VMs). No scale-to-zero.
- **Infrastructure:** Runs in Docker containers. Any language/version supported.
- **Machine Types:** Uses custom machine types (not N4/C4 - those are Compute Engine).
- **Capabilities:** Modify OS, access filesystem, SSH access.
- **Health Checks:**
  - `readiness_check`: When to route traffic to instance
  - `liveness_check`: When to restart unhealthy instance
- **Connectivity:** Easier VPC access than Standard.
- **Cost:** More expensive; min 1 instance always running.
- **Best For:** Apps with consistent traffic, custom dependencies, high CPU/memory needs.

## 3. App Engine Hierarchy

Understanding the relationship between components is essential for resource management.

1.  **Project:** The root Google Cloud resource.
2.  **Application:** The App Engine app within the project (one per project).
3.  **Service:** Microservices within the app (e.g., "frontend", "api", "worker").
4.  **Version:** Different versions of a service (e.g., "v1", "v2").
5.  **Instance:** The actual running units of a version.

## 4. Scaling Types

| Type      | Standard | Flexible | Description                               |
| --------- | -------- | -------- | ----------------------------------------- |
| Automatic | Yes      | Yes      | Based on CPU, throughput, latency targets |
| Basic     | Yes      | No       | On-demand; scale to zero when idle        |
| Manual    | Yes      | Yes      | Fixed instance count                      |

### Automatic Scaling Parameters

```yaml
automatic_scaling:
  target_cpu_utilization: 0.6 # Scale when CPU > 60%
  target_throughput_concurrent_requests: 100 # Or use this
  min_instances: 0 # Standard: 0 allows scale-to-zero
  max_instances: 10
```

### Basic Scaling Parameters

```yaml
basic_scaling:
  max_instances: 5
  idle_timeout: 60s # Shutdown after idle
```

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

- **`app.yaml`**: The core configuration file used for deployment. Defines runtime, scaling, handlers, and more.

### Standard Environment Example

```yaml
runtime: python312
instance_class: F2
automatic_scaling:
  min_instances: 1
  max_instances: 10
  target_cpu_utilization: 0.7

env_variables:
  ENV_NAME: "production"

handlers:
  - url: /static
    static_dir: static_files
  - url: /.*
    script: auto

warmup: enabled
```

### Flexible Environment Example

```yaml
runtime: java21
env: flex

automatic_scaling:
  min_num_instances: 1
  max_num_instances: 5

resources:
  cpu: 1
  memory_gb: 2
  disk_size_gb: 10

readiness_check:
  path: /ready
  check_interval_sec: 5
```

- **Deployment**: Use `gcloud app deploy`. By default, this promotes the new version to handle 100% of traffic. Use `--no-promote` to deploy without switching traffic.

## 7. Networking and Security

- **App Engine Firewall:** Control access by IP range (Allow or Deny).
- **IAP (Identity-Aware Proxy):** Restrict access based on IAM identities without modifying application code.
- **VPC Access:** Use a **Serverless VPC Access Connector** to reach resources with private IPs (Cloud SQL, Memorystore).
  - Flexible has easier VPC connectivity than Standard.
- **Service Accounts:**
  - Default: **App Engine Default Service Account** (`project-id@appspot.gserviceaccount.com`) with broad Editor permissions.
  - **Best Practice:** Create a custom service account with least-privilege permissions.
  - Use `--service-account=YOUR-SA@PROJECT.iam.gserviceaccount.com` in deployment.
- **Security Best Practices:**
  - Never use the default service account in production
  - Use IAP for user authentication
  - Leverage firewall rules for IP-based access control
  - Store secrets in Secret Manager, not in `app.yaml`

## 8. Essential `gcloud` Commands

| Command                                                              | Description                       |
| -------------------------------------------------------------------- | --------------------------------- |
| `gcloud app create --region [REGION]`                                | Initialize App Engine in a region |
| `gcloud app deploy [YAML_FILE]`                                      | Deploy application                |
| `gcloud app deploy --no-promote`                                     | Deploy without shifting traffic   |
| `gcloud app services set-traffic [SERVICE] --splits [V1=0.5,V2=0.5]` | Split traffic                     |
| `gcloud app browse`                                                  | Open app in browser               |
| `gcloud app logs read`                                               | View application logs             |
| `gcloud app versions list`                                           | List all versions                 |
| `gcloud app services list`                                           | List all services                 |
| `gcloud app instances list`                                          | List running instances            |

## 9. When to Use App Engine vs Alternatives

| Use Case                                   | Recommended Service |
| ------------------------------------------ | ------------------- |
| Traditional web apps, simple deployments   | App Engine Standard |
| Containerized microservices, scale-to-zero | Cloud Run           |
| Full Kubernetes control                    | GKE                 |
| Long-running processes, custom hardware    | Compute Engine      |
| App Engine Standard features + custom deps | App Engine Flexible |

### App Engine vs Cloud Run Quick Reference

| Feature           | App Engine    | Cloud Run              |
| ----------------- | ------------- | ---------------------- |
| Scale to zero     | Standard only | Yes                    |
| Container support | Flexible only | Yes (primary)          |
| Managed SSL       | Yes           | Yes                    |
| VPC access        | Via connector | Native (Knative)       |
| Warmup requests   | Yes           | No (cold starts)       |
| Minimum cost      | $0 (Standard) | ~$0 with scale-to-zero |

## 10. Cost Optimization Tips

1. **Use Standard environment** for intermittent traffic (scale-to-zero)
2. **Set appropriate `min_instances`** only when cold start latency is critical
3. **Choose correct instance classes** (F1-F4 vs M1-M2) based on your memory/CPU needs
4. **Use `target_cpu_utilization`** instead of throughput for more efficient scaling
5. **Deploy with `--no-promote`** when testing to avoid unnecessary traffic
6. **Delete unused versions** after migration

## 11. Exam Tips & Common Pitfalls

- **Region Lock:** Cannot change region after creation; must create new project.
- **Always deploy new versions** for major changes to enable instant rollbacks.
- **Warmup requests** reduce cold start latency (Standard environment).
- **Flexible requires at least 1 instance** - no scale-to-zero, factor this into cost.
- **Handlers order matters** - first matching handler wins.
- **Static files** must be served via handlers, not your application code.
- **App Engine API**: Use `google.appengine.application` for programmatic scaling config.

## 12. External Links

- [App Engine - Google Cloud Documentation](https://docs.cloud.google.com/appengine/docs)
- [App Engine Standard - Google Cloud](https://cloud.google.com/appengine/docs/standard)
- [App Engine Flexible - Google Cloud](https://cloud.google.com/appengine/docs/flexible)
- [Serverless VPC Access](https://cloud.google.com/vpc/docs/serverless-vpc-access)
- [Youtube - Andrew Brown - App Engine](https://www.youtube.com/watch?v=OlAmyf8_4O4&t=10827s)
- [App Engine - The Cloud Girl](https://www.thecloudgirl.dev/compute/app-engine)
