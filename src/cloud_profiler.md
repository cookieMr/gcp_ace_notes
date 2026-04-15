# Cloud Profiler: ACE Exam Study Guide (2026)

![Cloud Profiler](images/profiler.png)

_Image source: [Cloud Icons](https://cloud-icons.onemodel.app/gcp/networking/cloud_load_balancing)_

## 1. Cloud Profiler Overview

Cloud Profiler is a statistical, low-overhead tool that continuously profiles the performance of CPU, heap, and other resources in your applications.

- **Primary Purpose:** To identify specific functions or lines of code that are consuming the most resources (CPU, Memory) to optimize performance and reduce costs.
- **How it Works:** A small agent runs inside your application and sends profiling data to the Profiler backend.
- **Low Overhead:** Designed to run in _production_ with very low impact (typically less than 5%).

## 2. Key Concepts

- **Profile:** Data representing resource usage over a short period (default: 10 seconds, configurable).
- **Flame Graph:** The primary visualization tool.
  - **Width:** Represents the percentage of the resource consumed.
  - **Vertical Axis:** Shows function call hierarchy (parent functions at top, callees below).
- **Continuous Profiling:** Cloud Profiler is always-on in production.

- [Flame Graph - Google Cloud Documentation](https://docs.cloud.google.com/profiler/docs/concepts-flame)
- [Profiler Concepts - Google Cloud Documentation](https://cloud.google.com/profiler/docs/)

### Enabling the Profiler Agent

The profiler agent must be included in your application code:

| Environment          | How to Enable                                                |
| -------------------- | ------------------------------------------------------------ |
| Compute Engine / GKE | Install Cloud Profiler library and configure service account |
| App Engine           | Automatically available for supported runtimes               |
| Cloud Run            | Install Cloud Profiler library                               |
| Cloud Functions      | Install Cloud Profiler library                               |

### Java Example

Add Maven dependency:
```xml
<dependency>
    <groupId>com.google.cloud.profiler</groupId>
    <artifactId>cloud-profiler-java-agent</artifactId>
    <version>2.3.1</version>
</dependency>
```

Add to startup flags (VM options):
```bash
-javaagent:/path/to/cloud-profiler-java-agent.jar
```

Or via Spring Boot `application.properties`:
```
spring.cloud.gcp.profiler.enabled=true
```

- [Profiling Java - Google Cloud Documentation](https://docs.cloud.google.com/profiler/docs/profiling-java#standard-environment)

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
- **Heap:** Amount of memory currently in use (live objects only).
- **Allocated Heap:** Total memory allocated during profiling (includes freed objects) - useful for identifying memory leaks.
- **Heap Allocation Rate:** How fast memory is being allocated over time.
- **Threads:** Number of active threads.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/cloudprofiler.admin`: Full control over Profiler resources.
  - `roles/cloudprofiler.agent`: Allows the application's service account to send profiling data.
  - `roles/cloudprofiler.user`: Allows viewing and interacting with the UI.

## 6. Essential `gcloud` Commands

- **Enable API:** `gcloud services enable cloudprofiler.googleapis.com`
- **List profiles:** `gcloud profiler profiles list`
- **Profile data is primarily viewed via:** Cloud Console (Cloud Run → Profiler, or direct Profiler dashboard)

### Troubleshooting

| Issue               | Solution                                                           |
| ------------------- | ------------------------------------------------------------------ |
| No profiling data   | Check service account has `roles/cloudprofiler.agent`              |
| Agent not starting  | Verify the profiler library is correctly installed and initialized |
| Missing permissions | Ensure IAM roles are properly assigned to the service account      |

## 7. GCP Observability Tools Comparison

| Tool               | Purpose                         | What it Answers                              |
| ------------------ | ------------------------------- | -------------------------------------------- |
| **Cloud Profiler** | Code-level performance analysis | Which function is using the most CPU/memory? |
| **Cloud Trace**    | Distributed tracing             | Where is latency in my service calls?        |
| **Cloud Debugger** | Live debugging                  | What is the state of my code at this moment? |

## 8. Exam Tips

- **Profiler vs. Trace:**
  - **Cloud Trace:** Identifies latency bottlenecks _between_ services.
  - **Cloud Profiler:** Identifies performance issues _within_ a service (code level).
- **Production Use:** If a question mentions optimizing code in a production environment with minimal overhead, choose **Cloud Profiler**.
- **Agent Requirement:** Cloud Profiler **always requires** an agent or library to be included in your application code.
- **Dashboard Location:** Profiles are viewed in Cloud Console under **Cloud Profiler** or via **Cloud Run → Profiler**.

## 9. Practice Questions

**Q1:** A production service shows high CPU usage but you cannot reproduce it locally. Which GCP tool should you use?

> **Answer:** Cloud Profiler

**Q2:** You need to identify which function in your Java application is causing a memory leak. The service is running on GKE. What do you check?

> **Answer:** Cloud Profiler - look at Heap and Allocated Heap profiles

**Q3:** You want to understand why API response times are high across multiple microservices. What should you use?

> **Answer:** Cloud Trace (not Profiler - this is about inter-service latency, not code-level issues)
