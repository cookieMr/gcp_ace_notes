# Eventarc: ACE Exam Study Guide (2026)

![Eventarc](images/eventarc.png)

_Image source: Google Cloud Documentation_

## 1. Eventarc Overview

Eventarc is a fully managed eventing service that allows you to build event-driven architectures by routing events from various sources to specific destinations.

- Key Characteristics:
  - Fully Managed: No infrastructure to manage; scales automatically.
  - Decoupling: Enables asynchronous communication between producers and consumers.
  - Standardization: Uses the CloudEvents specification for a consistent event format.
  - Centralized: Provides a single place to manage triggers for events across your project.

## 2. Core Components

- Event: A record of something that happened (e.g., a file was uploaded to Cloud Storage).
- Trigger: A filter that defines which events should be sent to which destination.
- Destination (Target): The service that receives and processes the event (e.g., a Cloud Run service).
- Event Channel: A path to receive events from non-Google sources (like SaaS providers).

## 3. Event Sources

Eventarc can ingest events from a wide variety of sources:

- Direct Sources: Google Cloud services that support direct eventing (e.g., Cloud Storage, Pub/Sub).
- Cloud Audit Logs: Triggers based on administrative or data access actions in GCP. This allows you to react to services that don't have direct Eventarc support.
- Pub/Sub: Route existing Pub/Sub messages through Eventarc.
- Third-party (SaaS): External events from providers like Datadog, PagerDuty, etc.
- Custom Applications: Your own apps can publish events to Eventarc.

## 4. Event Destinations

Where the event can be sent for processing:

- Cloud Run (Services): The primary destination for Eventarc events.
- Cloud Functions (2nd Gen): 2nd Gen functions use Eventarc triggers to handle events.
- GKE (Google Kubernetes Engine): Events can be sent to services running in a GKE cluster.
- Workflows: Trigger a Google Cloud Workflow to orchestrate complex logic.
- Internal Load Balancers: Events can be routed through internal LBs to reach specific backends.

## 5. Protocols and Formats

- CloudEvents: Eventarc strictly follows the CloudEvents 1.0 specification.
- HTTP/JSON: Most events are delivered as HTTP POST requests with a JSON payload.

## 6. Security and IAM

- Service Accounts: You must specify a service account for the Eventarc trigger. This account needs:
  - Permissions to receive the event.
  - Permissions to invoke the destination service (e.g., `roles/run.invoker`).
- IAM Roles:
  - `roles/eventarc.admin`: Full control over triggers and channels.
  - `roles/eventarc.viewer`: View-only access.
  - `roles/eventarc.eventReceiver`: Required for a service account to receive events.

## 7. Essential `gcloud` Commands

- Create a Trigger (Cloud Storage):
  `gcloud eventarc triggers create [NAME] --destination-run-service=[SERVICE_NAME] --destination-run-region=[REGION] --event-filters="type=google.cloud.storage.object.v1.finalized" --event-filters="bucket=[BUCKET_NAME]" --service-account=[SA_EMAIL]`
- List Triggers:
  `gcloud eventarc triggers list --location=[LOCATION]`
- Describe a Trigger:
  `gcloud eventarc triggers describe [NAME] --location=[LOCATION]`
- Delete a Trigger:
  `gcloud eventarc triggers delete [NAME] --location=[LOCATION]`

## 8. Exam Tips

- 2nd Gen Cloud Functions: Eventarc is the engine behind 2nd Gen Cloud Functions triggers.
- Audit Logs for Everything: If a service doesn't have a direct source, use Cloud Audit Logs.
- Reliability: Eventarc provides at-least-once delivery. Destinations must be idempotent.
- Location: Triggers are regional and must be in the same region as the destination service.

## 9. Eventarc vs Pub/Sub

| Feature | Eventarc | Pub/Sub |
|---------|----------|---------|
| Model | Event-driven (pull/push) | Message-oriented (push/pull) |
| Event Format | CloudEvents (structured) | Any format (flexible) |
| Use Case | React to state changes | Async communication between services |
| Filtering | At trigger level (simple) | At subscription level (complex) |
| Source Type | GCP services, Audit Logs, SaaS | Any message publisher |
| Destination | Cloud Run, Cloud Functions, GKE, Workflows | Any HTTP endpoint, pull subscribers |

**When to use Pub/Sub:** For pure service-to-service messaging, high-throughput scenarios, or when you need more complex message filtering and transformation.

**When to use Eventarc:** When you want GCP-managed event routing with CloudEvents format, or when triggering serverless services (Cloud Run, Cloud Functions 2nd gen).

## 10. CloudEvents Format Details

Eventarc events follow the CloudEvents 1.0 specification with these key fields:

- `id`: Unique identifier for the event
- `source`: The origin of the event (e.g., `//storage.googleapis.com/buckets/my-bucket`)
- `type`: The event type (e.g., `google.cloud.storage.object.v1.finalized`)
- `datacontenttype`: MIME type of the data payload (usually `application/json`)
- `data`: The event-specific payload containing details about the change
- `time`: Timestamp of when the event occurred

Example CloudEvent payload from Cloud Storage:
```json
{
  "id": "test-event-id",
  "source": "//storage.googleapis.com/buckets/my-bucket",
  "type": "google.cloud.storage.object.v1.finalized",
  "datacontenttype": "application/json",
  "data": {
    "bucket": "my-bucket",
    "name": "my-file.txt",
    "metageneration": "1",
    "timeCreated": "2026-04-16T10:00:00Z",
    "updated": "2026-04-16T10:00:00Z"
  }
}
```

## 11. Event Filters

Triggers use event filters to determine which events to route. Filters use **AND logic** - all specified filters must match for the event to be delivered.

- **Single filter:** `--event-filters="type=google.cloud.storage.object.v1.finalized"`
- **Multiple filters:** `--event-filters="type=google.cloud.storage.object.v1.finalized" --event-filters="bucket=my-bucket"`
- **Common filter types:** `type`, `bucket`, `project`, `serviceName`, `methodName`

Filters can be applied to:
- Event type
- Event source (bucket, topic, etc.)
- Cloud Audit Log attributes (serviceName, methodName)

## 12. Event Channels

Event Channels provide a pathway to receive events from non-Google sources:

- **Purpose:** Bridge external event sources (SaaS, custom apps) into Eventarc
- **Creation:** `gcloud eventarc channels create [NAME] --location=[REGION]`
- **Activation:** Activate channels to receive events from providers like Datadog, PagerDuty
- **Use Case:** Integrating third-party monitoring, CI/CD, or custom application events

## 13. Cloud Audit Logs as Event Source

Cloud Audit Logs enable Eventarc to trigger on any GCP service action:

- **How it works:** Every GCP service writes to Cloud Audit Logs; Eventarc can listen to these logs and convert them to events
- **Available logs:** Admin Activity (always on), Data Access (configurable), System Events, Policy Denied
- **Filter by service:** Use `serviceName` filter to target specific GCP services (e.g., `compute.googleapis.com`)
- **Filter by method:** Use `methodName` filter for specific operations (e.g., `v1.compute.instances.insert`)

This makes Eventarc universal - any GCP operation can become an event trigger.

## 14. Failure Handling and Retry

- **At-least-once delivery:** Eventarc guarantees events will be delivered at least once
- **Retry behavior:** Failed deliveries are retried with exponential backoff
- **Destination failure:** If the destination is unavailable, Eventarc will retry until successful or until the event expires
- **Dead letter:** No native dead letter queue - handle failures in your destination service
- **Idempotency:** Destination services must be idempotent to handle duplicate deliveries

## 15. Event Discovery

Eventarc automatically discovers available events from GCP services:

- **Direct sources:** Services like Cloud Storage, Firestore, BigQuery have built-in event types
- **Audit Log sources:** Any GCP service that writes to Cloud Audit Logs can be an event source
- **Listing events:** `gcloud eventarc events list --location=[LOCATION]` shows available event types
- **Filter discovery:** Use `--filter` to find events matching specific criteria

## 16. Limitations and Quotas

- **Triggers per project:** Limited by quota (check Cloud Console)
- **Rate limits:** Events per second limits apply
- **Regional:** Triggers and channels are regional resources
- **Destination compatibility:** Not all destinations available in all regions
- **Filter complexity:** Limited to AND logic; complex filtering requires additional processing

## 17. Real-World Use Cases

- **Image processing:** Upload image to Cloud Storage → Eventarc triggers Cloud Run function to resize/process
- **System alerts:** Audit log event triggers Alertmanager or PagerDuty notification
- **Data pipeline:** BigQuery job completion triggers downstream processing
- **Database changes:** Firestore document update triggers notification or sync
- **Security monitoring:** Compute Engine instance creation triggers security scan
- **Cost optimization:** Daily Cloud Audit Log event triggers workflow to stop unused resources

## 18. Workflows as Destination

Eventarc can trigger Google Cloud Workflows for orchestrating complex logic:

- **Use case:** Multi-step processes that need coordination
- **Creation:** Use `--destination-workflow` flag in trigger creation
- **Input:** Event data is passed as input to the workflow
- **Example:** File upload → Eventarc → Workflow → Validate → Transform → Store → Notify
