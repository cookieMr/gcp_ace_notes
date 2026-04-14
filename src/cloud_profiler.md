# Cloud Profiler: ACE Exam Study Guide (2026)

![Cloud Profiler](images/profiler.png)

_Image source: [Cloud Icons](https://cloud-icons.onemodel.app/gcp/networking/cloud_load_balancing)_

## 1. Cloud Profiler Overview

Cloud Profiler is a statistical, low-overhead tool that continuously profiles the performance of CPU, heap, and other resources in your applications.

- **Primary Purpose:** To identify specific functions or lines of code that are consuming the most resources (CPU, Memory) to optimize performance and reduce costs.
- **How it Works:** A small agent runs inside your application and sends profiling data to the Profiler backend.
- **Low Overhead:** Designed to run in production with very low impact (typically less than 5%).

## 2. Key Concepts

- **Profile:** Data representing resource usage over a short period (usually 10 seconds).
- **Flame Graph:** The primary visualization tool.
  - **Width:** Represents the percentage of the resource consumed.
  - **Vertical Axis:** Represents the call stack.
- **Continuous Profiling:** Cloud Profiler is always-on in production.

---

- [Flame Graph - Google Cloud Documentation](https://docs.cloud.google.com/profiler/docs/concepts-flame)

## 3. Supported Environments and Languages

- **Supported Environments:**
  - Compute Engine (VMs)
  - Google Kubernetes Engine (GKE)
  - App Engine
  - Cloud Run
  - Cloud Functions
- **Supported Languages:**
  - Go, Java, Python, Node.js, C++.

## 4. Profile Types

Data collected depends on the language:

- **CPU Time:** Time spent by the CPU executing a function.
- **Wall Time:** Total time elapsed during execution (includes waiting for I/O).
- **Heap:** Amount of memory allocated in the heap.
- **Allocated Heap:** Total memory allocated during the profiling period.
- **Threads:** Number of active threads.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/cloudprofiler.admin`: Full control over Profiler resources.
  - `roles/cloudprofiler.agent`: Allows the application's service account to send profiling data.
  - `roles/cloudprofiler.user`: Allows viewing and interacting with the UI.

## 6. Essential `gcloud` Commands

- **Enable API:** `gcloud services enable cloudprofiler.googleapis.com`
- **Check Status:** Profiler is primarily managed via the Cloud Console UI.

## 7. Exam Tips

- **Profiler vs. Trace:**
  - **Cloud Trace:** Identifies latency bottlenecks _between_ services.
  - **Cloud Profiler:** Identifies performance issues _within_ a service (code level).
- **Production Use:** If a question mentions optimizing code in a production environment with minimal overhead, choose **Cloud Profiler**.
- **Agent Requirement:** Cloud Profiler **always requires** an agent or library to be included in your application code.
