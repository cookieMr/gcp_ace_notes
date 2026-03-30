# GCP Cloud Monitoring: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud Monitoring Overview

Cloud Monitoring provides visibility into the performance, uptime, and overall health of your applications and infrastructure.

- **Key Characteristics:**
  - **Full Stack:** Monitors GCP services, AWS, and on-premises resources.
  - **Integrated:** Collects metrics, events, and metadata from Cloud Logging, Trace, and Debugger.
  - **Real-time:** Provides a real-time dashboarding and alerting system.

## 2. Metrics and Time Series

- **System Metrics:** Automatically collected from GCP services (e.g., CPU, Disk I/O, Network traffic).
- **Custom Metrics:** Metrics you define and send to Monitoring via the API.
- **Log-based Metrics:** Metrics derived from the content of your logs in Cloud Logging.
- **Time Series:** The fundamental data structure in Monitoring, representing a series of data points over time.

## 3. Dashboards

Dashboards provide a visual representation of your metrics.

- **Google Cloud Dashboards:** Pre-defined dashboards created automatically for each GCP service you use.
- **Custom Dashboards:** Dashboards you create to monitor specific aspects of your application.
  - **Widgets:** You can add various widgets (charts, tables, gauges, scorecards) to visualize your data.
  - **MQL (Monitoring Query Language):** A powerful language used to create complex charts and data transformations.
- **Shared Dashboards:** You can share dashboards with other members of your project.

## 4. Alerting Policies

Alerting policies notify you when specific conditions are met, allowing you to respond to issues proactively.

- **Components of an Alerting Policy:**
  - **Conditions:** What triggers the alert (e.g., "CPU utilization > 80% for 5 minutes").
  - **Notification Channels:** How you are notified (e.g., Email, SMS, Slack, PagerDuty, Webhooks).
  - **Documentation:** Instructions or links to playbooks included in the alert notification.
- **Incident Management:** When an alert is triggered, an **incident** is created. You can track, acknowledge, and resolve incidents in the Monitoring console.

## 5. Uptime Checks

Uptime checks verify that your services are accessible from locations around the world.

- **Protocols:** Supports HTTP, HTTPS, and TCP.
- **Frequency:** You can define how often the check runs (e.g., every 1 minute).
- **Global Probes:** Checks are performed from multiple regions to ensure global availability.
- **Alerting Integration:** You can create an alerting policy to notify you if an uptime check fails.

## 6. Groups

Groups allow you to organize and monitor sets of resources together.

- **Criteria:** You can define groups based on names, tags, labels, or regions.
- **Subgroups:** Groups can contain other groups.
- **Use Case:** Monitor all web servers in the `us-east1` region as a single entity.

## 7. Essential gcloud Commands

- **List Metrics:** `gcloud monitoring metric-descriptors list`
- **Create a Dashboard:**
  `gcloud monitoring dashboards create --config-from-file=[DASHBOARD_JSON]`
- **List Alerting Policies:** `gcloud monitoring policies list`
- **Read Metric Data:** `gcloud monitoring time-series list --filter='metric.type="compute.googleapis.com/instance/cpu/utilization"'`

## 8. Exam Tips

- **Log-based Metric vs. System Metric:** Use log-based metrics when you need to count occurrences of a string in a log. Use system metrics for hardware/performance data.
- **Notification Channels:** Remember that you can use **Webhooks** to trigger automated responses (e.g., a Cloud Function) when an alert is fired.
- **Uptime Check Failures:** If an uptime check fails from one region but not others, it might be a regional networking issue rather than a service failure.
- **Agent vs. Agentless:** Basic metrics (CPU, Disk) are collected without an agent. For "inside-the-OS" metrics (e.g., Memory usage, internal process stats), you must install the **Ops Agent**.

[Back to root](./README.md)
