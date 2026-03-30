# GCP Cloud CDN: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud CDN Overview

Cloud CDN (Content Delivery Network) uses Google's global edge network to serve content closer to users, which reduces latency and lowers serving costs.

- **Key Characteristics:**
  - **Edge Caching:** Content is stored in Google's Edge Points of Presence (PoPs) globally.
  - **Integration:** Works exclusively with the **Global External Application Load Balancer** (HTTP/S).
  - **Origin Servers:** Backends can be Instance Groups (VMs), Cloud Storage buckets, or external origins.

## 2. Core Features

- **Anycast IP:** Uses a single, global IP address for the load balancer.
- **Cache Keys:** Defines what makes a request unique for caching purposes (e.g., URL, query parameters, headers).
- **Signed URLs/Cookies:** Used to serve private content only to authorized users (e.g., premium video or paid downloads).
- **Cache Invalidation:** Allows you to manually remove content from the cache before its TTL (Time To Live) expires.
- **Dynamic Compression:** Automatically compresses text-based responses (Gzip/Brotli) to save bandwidth.

## 3. Caching Behavior

- **TTL (Time To Live):** Defines how long content stays in the cache.
  - Default TTL: 3600 seconds (1 hour).
  - Maximum TTL: 31,536,000 seconds (1 year).
- **Cache Modes:**
  - **USE_ORIGIN_HEADERS:** Respects `Cache-Control` headers from the backend.
  - **CACHE_ALL_STATIC:** Caches all static content regardless of headers.
  - **FORCE_CACHE_ALL:** Unconditionally caches all responses (use with caution).

## 4. Administrative Operations

- **Enabling CDN:** You enable Cloud CDN on a per-backend-service basis within a Global External Application Load Balancer.
- **Invalidation:** `gcloud compute url-maps invalidate-cdn-cache [MAP_NAME] --path "/*"`
  - _Exam Tip:_ Cache invalidations are global but can take several minutes to propagate.

## 5. Security

- **SSL/TLS:** Handled at the Load Balancer level (SSL termination).
- **Cloud Armor integration:** You can use Cloud Armor security policies (WAF, Geo-blocking) in front of your CDN-enabled backends.

## 6. Essential gcloud Commands

- **Enable CDN on existing backend:**
  `gcloud compute backend-services update [BACKEND_NAME] --enable-cdn --global`
- **Invalidate a specific path:**
  `gcloud compute url-maps invalidate-cdn-cache [MAP_NAME] --path "/images/*"`
- **Describe backend CDN settings:**
  `gcloud compute backend-services describe [BACKEND_NAME] --global`

## 7. Exam Tips

- **Load Balancer Requirement:** Remember that Cloud CDN **cannot** be enabled on an Internal Load Balancer or a Network (L4) Load Balancer. It requires a **Global External Application Load Balancer**.
- **Static vs. Dynamic:** CDN is primarily for static content (images, CSS, JS). While it can cache dynamic content, it is less common.
- **Cost Savings:** Cloud CDN reduces "egress" costs because traffic from cache to user is cheaper than traffic from origin to user.
- **Cloud Storage:** When using Cloud Storage as a backend, ensure the bucket or files have public access (unless using Signed URLs).

[Back to root](./README.md)
