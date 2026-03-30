# GCP Cloud Trace: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud Trace Overview

Cloud Trace is a distributed tracing system that collects latency data from your applications and displays it in the Google Cloud Console.

- **Primary Purpose:** To understand the performance of your application and identify latency bottlenecks in microservices architectures.
- **How it Works:** It tracks how a single request travels through various services (e.g., frontend -> backend -> database) and records the time taken at each step.
- **Managed Service:** Fully managed, highly scalable, and requires minimal configuration for supported GCP services.

## 2. Key Concepts

- **Trace:** A set of spans that represent the complete path of a single request through your system.
- **Span:** A single operation within a trace (e.g., an RPC call, a database query, or a function execution). It includes start and end timestamps and metadata.
- **Latency Profile:** A visual representation (waterfall chart) showing where time was spent during a request's lifecycle.
- **Root Span:** The first span in a trace, representing the initial request.

## 3. Service Integration and Instrumentation

- **Automatic Integration:**
  - **App Engine (Standard and Flexible):** Traces are collected automatically for most applications.
  - **Cloud Run and Cloud Functions:** Basic tracing is enabled by default.
- **Manual Instrumentation:**
  - **Compute Engine and GKE:** Require the use of an SDK or agent (Google Cloud recommends **OpenTelemetry**) to send trace data to the Trace API.
  - **Internal Load Balancers:** Can also be configured to provide trace data.

## 4. Features and Analysis

- **Trace Explorer:** Used to search and visualize individual traces. You can filter by request URI, latency, or status code.
- **Analysis Reports:** Periodic reports (daily or on-demand) that compare the performance of your application over different time periods or across different versions.
- **Bottleneck Detection:** Identifies which service or operation is causing the most significant delay in a request.
- **Waterfall Charts:** Displays the sequence and duration of spans within a trace to pinpoint serial or parallel execution delays.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/cloudtrace.admin`: Full control over Cloud Trace resources.
  - `roles/cloudtrace.user`: Allows an application to send trace data to the API.
  - `roles/cloudtrace.viewer`: Allows viewing trace data and reports in the console.
- **Data Privacy:** Traces do not capture the body of the request or response by default, only metadata and timestamps.

## 6. Essential gcloud Commands

- **List Traces:** `gcloud alpha trace list` (Note: Most trace management is done via the Console or APIs).
- **Check API Status:** `gcloud services list --enabled | grep cloudtrace`

## 7. Exam Tips

- **Cloud Trace vs. Cloud Logging vs. Cloud Monitoring:**
  - **Logging:** "What happened?" (Text events).
  - **Monitoring:** "How is the system performing?" (Numerical metrics).
  - **Trace:** "Where is the delay?" (Latency distribution across services).
- **Performance Troubleshooting:** If an exam question asks how to find which microservice is slowing down a request, the answer is **Cloud Trace**.
- **Sampling Rate:** You can configure the sampling rate to control costs. Not every single request needs to be traced to get an accurate performance profile.
- **Propagation:** For distributed tracing to work, the trace ID must be passed between services in the HTTP headers (usually `X-Cloud-Trace-Context`).

[Back to root](./README.md)
