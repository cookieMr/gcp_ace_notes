# Memorystore: ACE Exam Study Guide (2026)

## 1. Memorystore Overview

Memorystore is Google Cloud’s fully managed in-memory data store service. It provides two main engines:

- Redis/Valkey: Feature-rich, persistent, HA-capable.
- Memcached: Simple, ephemeral, horizontally scalable cache.

Memorystore is used for low-latency caching, session storage, pub/sub, rate limiting, and real-time data access.

## 2. Supported Engines

### Redis/Valkey

- Sub-millisecond latency.
- Optional persistence (RDB snapshots).
- Supports replication and failover (Standard Tier).
- Supports Redis AUTH.
- Use cases: Caching, session storage, Pub/Sub, rate limiting, and distributed locks.
- Valkey: The 2026 standard for open-source high-performance caching in Google Cloud.

### Memcached

- Simple, in-memory cache.
- No persistence, no replication, no authentication.
- Horizontally scalable node pools.
- Use cases: Ephemeral caching, high-throughput simple key/value workloads.

## 3. Networking and Connectivity

Memorystore instances are VPC-only (no public IP).

### Connecting Serverless (Cloud Run / Functions)

- Direct VPC Egress (Recommended): Lowest latency, scales to zero cost, no VM management.
- Serverless VPC Access Connector: Legacy method; requires managing hidden connector VMs.

### Networking Models

- Standard/Basic Tiers: Use Private Service Access (PSA) (VPC Peering).
- Cluster/Valkey Tiers: Use Private Service Connect (PSC). Clients connect to a single IP (the discovery endpoint) in their own VPC.

| Service          | Can connect? | Requirements                 |
| ---------------- | ------------ | ---------------------------- |
| Compute Engine   | Yes          | Same VPC                     |
| GKE              | Yes          | Same VPC                     |
| Cloud Run        | Yes          | Direct VPC Egress (Rec)      |
| Cloud Functions  | Yes          | Direct VPC Egress (Rec)      |
| External clients | Yes          | Only via VPN or Interconnect |

## 4. Tiers and Availability

### Redis / Valkey Tiers

- Basic Tier: Single node, no replication, no failover. Good for dev/test.
- Standard Tier: Primary + replica, automatic failover. Recommended for production.

### Memcached Tiers

- Node pools with horizontal scaling. No persistence or failover.

## 5. Scaling Behavior

- Basic/Standard Tiers: Support Vertical Scaling (increase memory). This causes brief downtime.
- Cluster/Valkey Tiers: Support Horizontal Scaling (Sharding). You can add shards with zero downtime.
- Memcached: Scaled by adding/removing nodes from the pool.

## 6. Persistence and Backups

- Redis / Valkey: Supports RDB snapshots and point-in-time recovery (depending on tier). Persistence is optional.
- Memcached: No persistence; data is lost on restart or node failure.

## 7. Authentication and IAM

- IAM: Controls who can create/manage Memorystore instances.
- Redis AUTH: Optional password protection for clients.
- Memcached: No authentication.

## 8. Gemini and Monitoring

- Gemini for Memorystore: Use Gemini in the Cloud Console to analyze memory usage patterns and receive sharding recommendations for Redis/Valkey clusters.
- Cloud Monitoring: Integrate with Cloud Monitoring to track memory usage, CPU, connections, and evictions.

## 9. Common ACE Exam Scenarios

- Scenario: Connect Cloud Run to Redis with lowest cost/latency? Use Direct VPC Egress.
- Scenario: Scale Redis to 10TB+ with no downtime? Use Memorystore for Valkey or Redis Cluster.
- Scenario: Need high availability (HA)? Use Standard Tier (Primary + Replica).
- Scenario: Ephemeral cache with horizontal scaling? Use Memcached.
- Scenario: Running out of IPs in a peered VPC? Use PSC.

## 10. TTL (Time-to-Live)

TTL defines how long a key lives before Redis deletes it. It is essential for caching, sessions, and rate limiting.

- Set TTL on write: `SET key value EX 60`
- Set TTL after write: `EXPIRE key 60`

## 11. Quick Summary Table

| Feature         | Redis/Valkey                               | Memcached              |
| :-------------- | :----------------------------------------- | :--------------------- |
| **Persistence** | Yes (RDB/AOF)                              | No                     |
| **Scaling**     | Vertical (Standard) / Horizontal (Cluster) | Horizontal (Node pool) |
| **Networking**  | PSA (Standard) / PSC (Cluster)             | PSA                    |
| **Auth/TLS**    | Yes                                        | No                     |
| **Use Case**    | Advanced (Sessions, Pub/Sub, HA)           | Simple Key/Value Cache |
