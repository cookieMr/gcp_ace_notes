# Cloud Monitoring: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Cloud Monitoring Overview

Cloud Monitoring provides visibility into the performance, uptime, and overall health of your applications and infrastructure.

- **Key Characteristics:**
  - **Full Stack:** Monitors GCP services, AWS, and on-premises resources.
  - **Integrated:** Collects metrics, events, and metadata from Cloud Logging, Trace, and Debugger.
  - **Real-time:** Provides a real-time dashboarding and alerting system.
- **Gemini for Cloud Monitoring (2026 Update):** Gemini provides AI-driven insights for troubleshooting performance issues, explaining metric spikes, and recommending optimized alerting thresholds.

## 2. Metrics and Time Series

- **System Metrics:** Automatically collected from GCP services (e.g., CPU, Disk I/O).
- **Custom Metrics:** Metrics you define and send to Monitoring via the API.
- **Log-based Metrics:** Metrics derived from the content of your logs in Cloud Logging.
- **Time Series:** The fundamental data structure in Monitoring, representing data points over time.

## 3. Dashboards and MQL

Dashboards provide a visual representation of your metrics.

- **Google Cloud Dashboards:** Pre-defined dashboards created automatically.
- **Custom Dashboards:** Dashboards you create to monitor specific aspects of your application.
- **MQL (Monitoring Query Language):** A powerful language used to create complex charts and data transformations.
- **Gemini Assistant:** You can use natural language queries in the console to generate custom charts and dashboards using Gemini.

## 4. Alerting Policies

Alerting policies notify you when specific conditions are met.

- **Components of an Alerting Policy:**
  - **Conditions:** What triggers the alert (e.g., "CPU utilization > 80% for 5 minutes").
  - **Notification Channels:** How you are notified (Email, Slack, Webhooks).
  - **Documentation:** Instructions or links to playbooks included in the alert.
- **Incident Management:** When an alert is triggered, an **incident** is created for tracking and resolution.

## 5. Synthetic Monitoring (2026 Update)

Synthetic monitoring replaces traditional uptime checks with more complex, programmable checks.

- **Protocols:** Supports HTTP, HTTPS, and TCP.
- **Custom Scripts:** Use Node.js or Python scripts to simulate complex user journeys (e.g., "Login -> Add to Cart -> Checkout").
- **Global Probes:** Checks are performed from multiple regions around the world.
- **Alerting Integration:** Notify you if a synthetic check fails or exceeds latency thresholds.

## 6. Groups and Resources

Groups allow you to organize and monitor sets of resources together.

- **Criteria:** You can define groups based on names, tags, labels, or regions.
- **Use Case:** Monitor all web servers in the `us-east1` region as a single entity.

## 7. Essential gcloud Commands

- **List Metrics:** `gcloud monitoring metric-descriptors list`
- **Create a Dashboard:** `gcloud monitoring dashboards create --config-from-file=[DASHBOARD_JSON]`
- **List Alerting Policies:** `gcloud monitoring policies list`

## 8. Exam Tips

- **Log-based Metric vs. System Metric:** Use log-based metrics for counting log events. Use system metrics for performance data.
- **Ops Agent:** For "inside-the-OS" metrics like Memory usage and internal process stats, the **Ops Agent** must be installed on VMs.
- **Synthetic Monitoring:** If a question asks for testing a multi-step user flow from multiple regions, choose **Synthetic Monitoring**.

[Back to README](README.md)
