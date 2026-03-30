# GCP Eventarc: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Eventarc Overview

Eventarc is a fully managed eventing service that allows you to build event-driven architectures by routing events from various sources to specific destinations.

- **Key Characteristics:**
  - **Fully Managed:** No infrastructure to manage; scales automatically.
  - **Decoupling:** Enables asynchronous communication between producers and consumers.
  - **Standardization:** Uses the **CloudEvents** specification for a consistent event format.
  - **Centralized:** Provides a single place to manage triggers for events across your Google Cloud project.

## 2. Core Components

- **Event:** A record of something that happened (e.g., a file was uploaded to Cloud Storage).
- **Trigger:** A filter that defines which events should be sent to which destination.
- **Destination (Target):** The service that receives and processes the event (e.g., a Cloud Run service).
- **Event Channel:** (For third-party events) A path to receive events from non-Google sources (like SaaS providers).

## 3. Event Sources

Eventarc can ingest events from a wide variety of sources:

- **Direct Sources:** Specific Google Cloud services that support direct eventing (e.g., Cloud Storage, Pub/Sub).
- **Cloud Audit Logs:** Triggers based on almost any administrative or data access action in GCP. (This allows you to react to services that don't have direct Eventarc support).
- **Pub/Sub:** You can route existing Pub/Sub messages through Eventarc.
- **Third-party (SaaS):** Support for external events from providers like Datadog, PagerDuty, etc.
- **Custom Applications:** Your own apps can publish events to Eventarc.

## 4. Event Destinations

Where the event can be sent for processing:

- **Cloud Run (Services):** The primary destination for Eventarc events.
- **Cloud Functions (2nd Gen):** Under the hood, 2nd Gen functions use Eventarc triggers to handle events.
- **GKE (Google Kubernetes Engine):** Events can be sent to services running in a GKE cluster.
- **Workflows:** You can trigger a Google Cloud Workflow to orchestrate complex logic in response to an event.
- **Internal Load Balancers:** Events can be routed through internal LBs to reach specific backends.

## 5. Protocols and Formats

- **CloudEvents:** Eventarc strictly follows the CloudEvents 1.0 specification. This ensures that the event metadata (source, type, ID) is consistent regardless of the source.
- **HTTP/JSON:** Most events are delivered as HTTP POST requests with a JSON payload.

## 6. Security and IAM

- **Service Accounts:** You must specify a service account for the Eventarc trigger. This account needs:
  - Permissions to receive the event.
  - Permissions to invoke the destination service (e.g., `roles/run.invoker`).
- **IAM Roles:**
  - `roles/eventarc.admin`: Full control over triggers and channels.
  - `roles/eventarc.viewer`: View-only access.
  - `roles/eventarc.eventReceiver`: Required for a service account to receive events from a provider.

## 7. Essential gcloud Commands

- **Create a Trigger (Cloud Storage):**
  `gcloud eventarc triggers create [NAME] --destination-run-service=[SERVICE_NAME] --destination-run-region=[REGION] --event-filters="type=google.cloud.storage.object.v1.finalized" --event-filters="bucket=[BUCKET_NAME]" --service-account=[SA_EMAIL]`
- **List Triggers:**
  `gcloud eventarc triggers list --location=[LOCATION]`
- **Describe a Trigger:**
  `gcloud eventarc triggers describe [NAME] --location=[LOCATION]`
- **Delete a Trigger:**
  `gcloud eventarc triggers delete [NAME] --location=[LOCATION]`

## 8. Exam Tips

- **2nd Gen Cloud Functions:** Remember that Eventarc is the engine behind 2nd Gen Cloud Functions triggers. If a question asks how 2nd Gen functions handle advanced event filtering, the answer is **Eventarc**.
- **Audit Logs for Everything:** If a GCP service doesn't have a direct "Eventarc Source," you can almost always trigger an event using **Cloud Audit Logs** for that service.
- **Cross-Project Events:** Eventarc supports receiving events from one project and sending them to a destination in another project (requires proper IAM setup).
- **Reliability:** Eventarc provides **at-least-once delivery**. Your destination services must be **idempotent**.
- **Location:** Triggers are regional. The trigger must be in the same region as the destination service.

[Back to root](./README.md)
