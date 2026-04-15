# Error Reporting: ACE Exam Study Guide (2026)

![Error Reporting](images/error_reporting.png)

_Image source: Google Cloud Documentation_

## 1. Error Reporting Overview

Cloud Error Reporting aggregates and displays errors from your running cloud services.

- **Primary Purpose:** To provide a centralized view of application errors, crash reports, and exceptions to help you identify, prioritize, and resolve issues quickly.
- **Automatic Aggregation:** It automatically groups similar errors based on their stack traces and context.
- **Real-time Alerts:** You can receive notifications (Email, SMS, Mobile) when a new error is detected or an existing error occurs frequently.

## 2. Key Concepts

- **Error Group:** A collection of individual error occurrences that share a similar cause (e.g., the same stack trace).
- **Resolution Status:** Track error groups as **Open**, **Acknowledged**, **Resolved**, or **Muted**.
- **First and Last Seen:** Timestamps that help you understand when a bug was introduced and how recently it occurred.
- **Impact Analysis:** Shows how many users are affected by a specific error and how many occurrences have happened.
- **Error Rate:** Number of errors per minute/second - helps identify error spikes.

### Error Tracking Workflow

```
Application throws exception
         ↓
Logs written to stdout/stderr (in structured format)
         ↓
Cloud Logging captures logs
         ↓
Error Reporting parses and groups similar errors
         ↓
Dashboard displays aggregated error groups
```

### Required Error Format

Error Reporting expects errors in structured log format:

```json
{
  "severity": "ERROR",
  "message": "java.lang.NullPointerException: Cannot invoke method",
  "logging.googleapis.com/labels": {
    "error_group": "group_id"
  }
}
```

| Environment         | Format Method                                 |
| ------------------- | --------------------------------------------- |
| App Engine          | Automatic (managed runtimes)                  |
| Cloud Run/Functions | Write to stderr in text or JSON format        |
| GKE/Compute Engine  | Use Error Reporting API or structured logging |

## 3. Supported Sources

Error Reporting can collect errors from several sources:

- **App Engine:** Integrated automatically for supported languages.
- **Cloud Run and Cloud Functions:** Errors written to `stdout` or `stderr` in a supported format are automatically captured.
- **GKE and Compute Engine:**
  - Errors can be captured from logs in Cloud Logging if they are in a supported format.
  - Alternatively, use the **Error Reporting API** directly from your application code.

```java
import com.google.cloud.errorreporting.v1beta1.ReportErrorsServiceClient;
import com.google.devtools.clouderrorreporting.v1beta1.ReportedErrorEvent;
import com.google.devtools.clouderrorreporting.v1beta1.ErrorContext;
import com.google.devtools.clouderrorreporting.v1beta1.SourceLocation;

try {
    // Your code that throws an exception
} catch (Exception e) {
    try (var client = ReportErrorsServiceClient.create()) {
        var error = ReportedErrorEvent.newBuilder()
            .setMessage(e.toString())
            .setContext(ErrorContext.newBuilder()
                .setReportLocation(SourceLocation.newBuilder()
                    .setFilePath("MyClass.java")
                    .setLineNumber(42)
                    .setFunctionName("myMethod")
                    .build())
                .build())
            .build();
        client.reportProjectOwnershipError(error);
    }
}
```

_Java example: Reporting errors via Error Reporting API_

- **Cloud Logging:** You can configure Error Reporting to watch specific logs for exceptions.

## 4. Supported Languages

Error Reporting supports major languages including: Go, Java, Python, Node.js, Ruby, PHP and .NET.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/errorreporting.admin`: Full control over Error Reporting resources.
  - `roles/errorreporting.writer`: Permission to send error data to the API.
  - `roles/errorreporting.viewer`: Permission to view error reports in the console.
  - `roles/errorreporting.user`: Combined viewer and writer permissions.

## 6. Integration with Other Services

- **Cloud Logging:** Primary ingestion method - errors written to logs are automatically parsed.
- **Cloud Monitoring:** Create alerting policies based on error frequencies (errors per minute threshold).
- **Issue Trackers:** Link error groups to external trackers (Jira, GitHub Issues) directly from the console.
- **Cloud Trace:** Correlate errors with latency data to identify if errors cause performance issues.

## 7. Essential `gcloud` Commands

- **Enable API:** `gcloud services enable clouderrorreporting.googleapis.com`
- **List Error Groups:** `gcloud alpha error-reporting groups list`
- **Check API Status:** `gcloud services list --enabled | grep clouderrorreporting`

### Console Location

Errors are viewed in Cloud Console:

- **Direct path:** Cloud Console → Logging → Error Reporting
- **From Cloud Run:** Your service → Errors tab
- **From App Engine:** App Engine → Dashboard → Errors

### Troubleshooting

| Issue                | Solution                                                  |
| -------------------- | --------------------------------------------------------- |
| Errors not appearing | Check logs are written to stderr/stdout in correct format |
| Missing error groups | Verify Error Reporting API is enabled                     |
| Permission denied    | Ensure service account has `roles/errorreporting.writer`  |

## 8. GCP Observability Tools Comparison

| Tool                | Purpose                      | What it Answers                              |
| ------------------- | ---------------------------- | -------------------------------------------- |
| **Cloud Logging**   | Log aggregation and analysis | What happened at a specific point in time?   |
| **Error Reporting** | Aggregated error tracking    | What bugs are in my code?                    |
| **Cloud Debugger**  | Live debugging               | What is the state of my code at this moment? |

## 9. Exam Tips

- **Error vs. Log:**
  - **Cloud Logging:** Individual text events over time.
  - **Error Reporting:** Aggregated stack traces and exceptions.
- **Troubleshooting:** If a question asks for a centralized way to track and manage application crashes or exceptions, the answer is **Error Reporting**.
- **Automatic Groups:** Error Reporting **groups** errors intelligently based on stack trace - saves developers from redundant logs.
- **Notification:** To get notified when a new error occurs, use Error Reporting's built-in notification feature.
- **Issue Linking:** Link errors to external bug trackers (Jira, GitHub Issues) directly from the console.
- **Mute Behavior:** Muting an error group suppresses notifications but does not delete the error data.
