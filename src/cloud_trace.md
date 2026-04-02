# Cloud Trace: ACE Exam Study Guide (2026)

## 1. Cloud Trace Overview

Cloud Trace is a distributed tracing system that collects latency data from your applications and displays it in the Google Cloud Console.

- **Primary Purpose:** To understand the performance of your application and identify latency bottlenecks in microservices architectures.
- **How it Works:** It tracks how a single request travels through various services (frontend, backend, database) and records the time taken at each step.
- **Gemini Integration:** Gemini in the Cloud Console can perform root cause analysis by summarizing trace data and identifying specific code spans responsible for performance regressions.

## 2. Key Concepts

- **Trace:** A set of spans representing the complete path of a single request through your system.
- **Span:** A single operation within a trace (e.g., an RPC call or a database query) with start and end timestamps.
- **Latency Profile:** A waterfall chart showing where time was spent during a request's lifecycle.
- **Root Span:** The first span in a trace, representing the initial request.

## 3. Service Integration and Instrumentation

- **Automatic Integration:**
  - **App Engine (Standard and Flexible):** Traces are collected automatically.
  - **Cloud Run and Cloud Functions:** Basic tracing is enabled by default.
- **Manual Instrumentation:**
  - **Compute Engine and GKE:** Requires use of an SDK or agent (Google recommends **OpenTelemetry**) to send data to the Trace API.
  - **Internal Load Balancers:** Can be configured to provide trace data.
  - **Multi-Cloud (2026 Standard):** Support for traces across GCP, AWS, and Azure via OpenTelemetry.

## 4. Features and Analysis

- **Trace Explorer:** Search and visualize individual traces. Filter by URI, latency, or status code.
- **Analysis Reports:** Periodic reports that compare performance across different versions or time periods.
- **Bottleneck Detection:** Identifies which operation is causing the most significant delay.
- **Waterfall Charts:** Displays the sequence and duration of spans to pinpoint serial execution delays.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/cloudtrace.admin`: Full control over Cloud Trace resources.
  - `roles/cloudtrace.user`: Allows an application to send trace data to the API.
  - `roles/cloudtrace.viewer`: Allows viewing trace data and reports in the console.

## 6. Essential gcloud Commands

- **Check API Status:** `gcloud services list --enabled | grep cloudtrace`
- **List Traces:** Use the console for visual management, but be aware of the `gcloud alpha trace` group for metadata.

## 7. Exam Tips

- **Cloud Trace vs. Cloud Logging vs. Cloud Monitoring:**
  - **Logging:** "What happened?" (Text events).
  - **Monitoring:** "How is the system performing?" (Numerical metrics).
  - **Trace:** "Where is the delay?" (Latency distribution across services).
- **Performance Troubleshooting:** If a question asks how to find which microservice is slowing down a request, the answer is **Cloud Trace**.
- **Sampling Rate:** You can configure the sampling rate to control costs while maintaining an accurate performance profile.
- **Propagation:** The trace ID must be passed between services in the HTTP headers (usually `X-Cloud-Trace-Context`).
