# Google Cloud Memorystore — ACE Exam Study Guide

[Back to root](./README.md)

## Overview

**Memorystore** is Google Cloud’s fully managed in‑memory data store service.  
It provides two engines:

- **Redis/Valkey** — feature‑rich, persistent, HA‑capable
- **Memcached** — simple, ephemeral, horizontally scalable cache

Memorystore is used for low‑latency caching, session storage, pub/sub, rate limiting, and real‑time data access.

---

# Key Concepts You Must Know for ACE

## 1. Supported Engines

### Redis/Valkey

- Sub‑millisecond latency
- Optional persistence (RDB snapshots)
- Supports replication + failover (Standard Tier)
- Supports Redis AUTH
- Use cases:
  - Caching
  - Session storage
  - Pub/Sub
  - Rate limiting
  - Distributed locks

### Memcached

- Simple, in‑memory cache
- No persistence
- No replication
- No authentication
- Horizontally scalable node pools
- Use cases:
  - Ephemeral caching
  - High‑throughput simple key/value workloads

---

## 2. Networking & Connectivity

Memorystore instances are **VPC-only** (no public IP).

### Connecting Serverless (Cloud Run / Functions)

For 2026 exams, know the two methods:

1.  **Direct VPC Egress (Recommended):** Lowest latency, scales to zero cost, no VM management.
2.  **Serverless VPC Access Connector:** Legacy method; requires managing hidden connector VMs.

### Networking Models

- **Standard/Basic Tiers:** Use **Private Service Access (PSA)** (VPC Peering).
- **Cluster/Valkey Tiers:** Use **Private Service Connect (PSC)**. Clients connect to a single IP (the discovery endpoint) in their own VPC.

### Connecting from GCP services

| Service          | Can connect? | Requirements                 |
| ---------------- | ------------ | ---------------------------- |
| Compute Engine   | ✔️           | Same VPC                     |
| GKE              | ✔️           | Same VPC                     |
| Cloud Run        | ✔️           | **Direct VPC Egress** (Rec)  |
| Cloud Functions  | ✔️           | **Direct VPC Egress** (Rec)  |
| External clients | ✔️           | Only via VPN or Interconnect |

#### **Exam trap:**

> Serverless products **cannot** reach Memorystore without a VPC connection (Direct VPC Egress or Connector).

---

## 3. Tiers & Availability

## Redis / Valkey Tiers

### Basic Tier

- Single node
- No replication
- No failover
- Lower cost
- Good for dev/test

### Standard Tier

- Primary + replica
- Automatic failover
- Higher availability
- Recommended for production

## Memcached

- Node pools
- Horizontal scaling
- No persistence
- No failover

---

## 4. Scaling Behavior

- **Basic/Standard Tiers:** Support **Vertical Scaling** (increase memory). This causes **brief downtime**.
- **Cluster/Valkey Tiers:** Support **Horizontal Scaling (Sharding)**. You can add shards to increase capacity and throughput with **zero downtime**.
- **Memcached:** Scaled by adding/removing nodes from the pool.

---

## 5. Persistence & Backups

### Redis / Valkey

- Supports **RDB snapshots**
- Supports **backups** and **point‑in‑time recovery** (depending on tier)
- Persistence is optional

### Memcached

- **No persistence**
- Data is lost on restart or node failure

---

## 6. Authentication & IAM

### IAM

- Controls **who can create/manage** Memorystore instances
- Does **not** control data‑level access

### Redis AUTH

- Optional
- Clients must provide password if enabled

### Memcached

- No authentication

---

## 7. Monitoring & Logging

Memorystore integrates with:

- **Cloud Monitoring**
  - Memory usage
  - CPU
  - Connections
  - Evictions
- **Cloud Logging**
  - Audit logs for API operations

---

## 8. Common ACE Exam Scenarios

- **Scenario: Connect Cloud Run to Redis with lowest cost/latency?** → Use **Direct VPC Egress**.
- **Scenario: Scale Redis to 10TB+ with no downtime?** → Use **Memorystore for Valkey** or **Redis Cluster**.
- **Scenario: Connect serverless to a Cluster instance?** → Use a **Private Service Connect (PSC)** endpoint.
- **Scenario: Need high availability (HA)?** → Use **Standard Tier** (Primary + Replica).
- **Scenario: Ephemeral cache with horizontal scaling?** → **Memcached**.
- **Scenario: Running out of IPs in a peered VPC?** → Use **PSC** (consumes fewer IPs than PSA).

---

## 9. Connecting from a Spring Boot App (Example)

```properties
spring.data.redis.host=10.45.123.17
spring.data.redis.port=6379
spring.data.redis.password=
spring.data.redis.timeout=2000
```

Example usage:

```java
@Service
@RequiredArgsConstructor
public class CacheService {
    private final StringRedisTemplate redis;

    public void save(String key, String value) {
        redis.opsForValue().set(key, value);
    }

    public String get(String key) {
        return redis.opsForValue().get(key);
    }
}
```

---

## 10. TTL (Time‑to‑Live)

TTL defines how long a key lives before Redis deletes it.

- Set TTL on write:  
  `SET key value EX 60`
- Set TTL after write:  
  `EXPIRE key 60`
- Check TTL:  
  `TTL key`

TTL is essential for:

- Caching
- Sessions
- Rate limiting
- Distributed locks

---

## 11. Quick Memorystore Summary Table

| Feature         | Redis/Valkey                               | Memcached              |
| :-------------- | :----------------------------------------- | :--------------------- |
| **Persistence** | ✔️ (RDB/AOF)                               | ❌                     |
| **Scaling**     | Vertical (Standard) / Horizontal (Cluster) | Horizontal (Node pool) |
| **Networking**  | PSA (Standard) / PSC (Cluster)             | PSA                    |
| **Auth/TLS**    | ✔️                                         | ❌                     |
| **Use Case**    | Advanced (Sessions, Pub/Sub, HA)           | Simple Key/Value Cache |

---

## Final Exam Tips

- **Direct VPC Egress** is the modern serverless-to-VPC path.
- **Valkey** is the open-source alternative to Redis.
- **Cluster mode** = Horizontal scaling (sharding).
- **PSC** is the networking standard for modern Clusters.
- **Standard Tier** = High Availability (HA).
- **Memorystore is private-only** (no public internet access).

[Back to root](./README.md)
