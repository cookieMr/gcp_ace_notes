# DevOps, Monitoring, and Management

![DevOps](images/DevOps-512-color.png)

_Image source: Google Cloud Documentation_

### **[Cloud Logging](./cloud_logging.md)**

Fully managed log aggregation and analysis service. Collects, stores, and searches logs from all GCP services and custom applications with 30-day retention and severity levels.

### **[Cloud Monitoring](./cloud_monitoring.md)**

Unified observability platform for metrics, dashboards, and alerting. Monitors GCP, AWS, and on-premises resources with real-time visibility into performance and uptime.

### **[Cloud Trace](./cloud_trace.md)**

Distributed tracing service that captures request latency across microservices. Visualizes end-to-end request paths with spans to identify performance bottlenecks.

### **[Cloud Profiler](./cloud_profiler.md)**

Statistical profiling tool for production environments. Continuously analyzes CPU and memory usage with flame graphs to identify resource-intensive code paths.

### **[Error Reporting](./error_reporting.md)**

Centralized error aggregation and notification service. Groups similar errors by stack trace, tracks resolution status, and alerts on new or escalating error patterns.

### **[Cloud Build](./cloud_build.md)**

Serverless CI/CD platform that executes builds in containers. Runs build steps defined in cloudbuild.yaml with triggers for automated deployments from source repositories.

### **[Artifact Registry](./artifact_registry.md)**

Universal artifact repository for container images and language packages. Stores Docker, Maven, npm, and Python artifacts with vulnerability scanning and fine-grained IAM.

### **[Deployment Manager](./deployment_manager.md)**

Infrastructure as Code service for automating GCP resource creation. Uses Jinja2 or Python templates to declaratively define and manage infrastructure deployments.

### **[Cloud Scheduler](./cloud_scheduler.md)**

Fully managed cron job service for scheduled task execution. Triggers HTTP, Pub/Sub, or App Engine targets on configurable schedules with at-least-once delivery.

### **[Pub/Sub](./pubsub.md)**

Global serverless messaging service for asynchronous event-driven communication. Decouples publishers from subscribers with at-least-once delivery and automatic scaling.

### **[Eventarc](./eventarc.md)**

Managed event routing service that connects event sources to destinations. Uses CloudEvents format to route GCP, Audit Log, or custom events to Cloud Run, Functions, or Workflows.
