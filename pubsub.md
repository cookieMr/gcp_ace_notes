# GCP Pub/Sub: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Pub/Sub Overview

Pub/Sub is a global, asynchronous, and scalable messaging service that decouples services that produce events (Publishers) from services that process events (Subscribers).

- **Key Characteristics:**
  - **Global Service:** Topics and subscriptions are global resources.
  - **Serverless:** Automatically scales to handle millions of messages per second.
  - **Asynchronous:** Publishers don't wait for subscribers to receive messages.
  - **Decoupling:** Allows independent scaling of producers and consumers.

## 2. Core Concepts

- **Topic:** A named resource to which publishers send messages.
- **Subscription:** A named resource representing the stream of messages from a single, specific topic, to be delivered to the subscribing application.
- **Message:** The combination of data and (optional) attributes that a publisher sends to a topic and Pub/Sub delivers to subscribers.
- **Publisher:** An application that creates and sends messages to a topic.
- **Subscriber:** An application that registers itself to a subscription to receive messages.

## 3. Subscription Types (Critical for Exam)

Pub/Sub supports several ways to deliver messages:

- **Pull Subscription:**
  - The subscriber application initiates requests to the Pub/Sub server to retrieve messages.
  - Best for: High-throughput jobs, large batch processing, or when the subscriber is behind a firewall.
- **Push Subscription:**
  - Pub/Sub initiates an HTTP POST request to a pre-defined endpoint (Webhook).
  - Best for: Serverless environments like **Cloud Run** or **Cloud Functions**.
  - Requires a verified domain or a service account with the `roles/run.invoker` role.
- **BigQuery Subscription:**
  - Pub/Sub writes messages directly into a BigQuery table.
  - No intermediate code/subscriber needed.
- **Cloud Storage Subscription:**
  - Pub/Sub writes messages directly to a Cloud Storage bucket as objects.

## 4. Message Reliability and Lifecycle

- **At-least-once Delivery:** Pub/Sub guarantees that a message will be delivered at least once. It does **not** guarantee exactly-once delivery (unless specifically configured with Exactly-once delivery enabled).
  - _Exam Tip:_ Applications must be **idempotent** to handle duplicate messages.
- **Acknowledgement (ACK):** Once a subscriber processes a message, it sends an ACK. Pub/Sub then removes the message from the subscription.
- **Nack (Negative Ack):** Tells Pub/Sub to redeliver the message immediately.
- **Ack Deadline:** The time a subscriber has to acknowledge a message before Pub/Sub attempts redelivery (default is 10 seconds).
- **Message Retention:** Messages are stored in a subscription for up to **7 days** (default) if they are not acknowledged.

## 5. Advanced Features

- **Dead Letter Topics:** If a message cannot be processed after multiple attempts, it is sent to a "Dead Letter Topic" for investigation.
- **Message Ordering:** Messages can be delivered in the order they were published if an **Ordering Key** is provided and the feature is enabled on the subscription.
- **Filtering:** Subscriptions can specify a filter so that they only receive messages that match specific attributes (saves cost and processing).
- **Replay (Seek):** Allows you to "rewind" a subscription to a specific point in time to reprocess messages.

## 6. Security and IAM

- **Roles:**
  - `roles/pubsub.publisher`: Permission to send messages to a topic.
  - `roles/pubsub.subscriber`: Permission to pull messages from a subscription and acknowledge them.
  - `roles/pubsub.viewer`: View-only access to topics and subscriptions.
  - `roles/pubsub.admin`: Full control.
- **Service Accounts:** Used by Pub/Sub to push messages to authenticated endpoints or write to BigQuery/GCS.

## 7. Essential gcloud Commands

- **Create a Topic:**
  `gcloud pubsub topics create [TOPIC_NAME]`
- **Create a Pull Subscription:**
  `gcloud pubsub subscriptions create [SUB_NAME] --topic=[TOPIC_NAME]`
- **Create a Push Subscription:**
  `gcloud pubsub subscriptions create [SUB_NAME] --topic=[TOPIC_NAME] --push-endpoint=[URL]`
- **Publish a Message:**
  `gcloud pubsub topics publish [TOPIC_NAME] --message="Hello World"`
- **Pull a Message Manually:**
  `gcloud pubsub subscriptions pull [SUB_NAME] --auto-ack`

## 8. Exam Tips

- **Decoupling:** If the question mentions scaling two parts of an application independently, the answer is usually **Pub/Sub**.
- **Buffering:** Use Pub/Sub to buffer spikes in traffic (e.g., IoT data ingestion) before processing it with Dataflow or BigQuery.
- **Global vs Regional:** Pub/Sub is **Global**. You don't specify a zone for a topic.
- **Fan-out:** One topic can have many subscriptions. Each subscription receives a copy of every message published to the topic.
- **Push vs Pull:** Use **Push** for low-latency webhooks and serverless. Use **Pull** for high-volume worker fleets.

[Back to README](README.md)
