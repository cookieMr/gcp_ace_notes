# Memorystore: ACE Exam Study Guide (2026)

![Memorystore](images/memorystore.png)

_Image source: Google Cloud Documentation_

## 1. Memorystore Overview

Memorystore is Google Cloud’s fully managed in-memory data store service. It is used for low-latency caching, session storage, and real-time data access.

## 2. Redis/Valkey vs. Memcached

| Feature          | Redis / Valkey                                              | Memcached                                |
| ---------------- | ----------------------------------------------------------- | ---------------------------------------- |
| **Deployment**   | **Regional** (multi‑zone)                                   | **Zonal** (no multi‑zone replication)    |
| **Availability** | Standard Tier: automatic failover (Primary → Replica)       | No HA, no failover                       |
| **Persistence**  | Optional: RDB snapshots + point‑in‑time recovery            | None (purely in‑memory, ephemeral)       |
| **Use Case**     | Durable cache, counters, queues, sessions, Pub/Sub patterns | Large, simple, ephemeral key/value cache |
| **Scaling**      | Vertical (Basic/Standard) or Horizontal (Cluster mode)      | Horizontal (node pool)                   |
| **Networking**   | PSA (Standard) / PSC (Cluster mode)                         | PSA (VPC Peering)                        |
| **Auth/TLS**     | Yes (AUTH, TLS)                                             | No                                       |
| **Notes**        | Valkey = modern Redis‑compatible engine (2026+)             | Best when data loss is acceptable        |

## 3. Networking and Connectivity

Memorystore instances are **VPC-only** (no public IPs).

### Connecting Serverless (Cloud Run / Functions)

- **Direct VPC Egress (Recommended):** Lowest latency and cost.
- **Serverless VPC Access Connector:** Legacy method.

### Networking Models

- **Standard/Basic Tiers:** Use Private Service Access (PSA).
- **Cluster/Valkey Tiers:** Use **Private Service Connect (PSC)**. Clients connect to a single IP (discovery endpoint) in their own VPC.

> _Private Service Access_ lets your VPC connect privately to Google‑managed services that run inside your project, such as _Cloud SQL_, _**Memorystore**_, _AlloyDB_, and _Filestore_. It works through _VPC peering_ and a reserved IP range, giving those services private [RFC 1918](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) addresses. PSA is regional and meant for accessing Google‑managed backends you own.

> _Private Service Connect_ creates private endpoints that let your VPC reach Google APIs, third‑party SaaS, or services in other projects using private IPs. It uses Google’s internal load balancing instead of VPC peering, making it ideal for cross‑project or cross‑organization service consumption or publishing.

| Service          | Can connect? | Requirements                 |
| ---------------- | ------------ | ---------------------------- |
| Compute Engine   | Yes          | Same VPC                     |
| GKE              | Yes          | Same VPC                     |
| Cloud Run        | Yes          | Direct VPC Egress            |
| External clients | Yes          | Only via VPN or Interconnect |

## 4. Scaling and TTL

- **Scaling:**
  - **Vertical:** Increasing memory on Basic/Standard tiers causes brief downtime.
  - **Horizontal:** Adding shards (Cluster/Valkey) or nodes (Memcached) has zero downtime.
- **TTL (Time-to-Live):** Essential for cache management.
  - `SET key value EX 60` (Set on write)
  - `EXPIRE key 60` (Set after write)
    > TTL is simply an expiration timer for a key. When you set a TTL, Redis automatically deletes the key after the specified number of seconds. It’s used to control cache freshness, prevent stale data, and free memory without manual cleanup.

## 5. Authentication and Monitoring

- **Security:**
  - **IAM:** Controls management plane (creating/deleting instances).
  - **Redis AUTH:** Application-level password (not IAM-based). Must be enabled at creation.

## 6. Common ACE Exam Scenarios

- **Scenario**: Connect Cloud Run to Redis with lowest cost? → Use **Direct VPC Egress**.
- **Scenario**: Scale Redis to 10TB+ with zero downtime? → Use **Redis Cluster** or **Valkey**.
- **Scenario**: Need High Availability (HA)? → Use **Standard Tier** (Primary + Replica).
- **Scenario**: Ephemeral cache for simple KV pairs? → Use **Memcached**.
- **Scenario**: Avoid VPC Peering limits? → Use **Private Service Connect (PSC)**.

## 7. Using Memorystore in Spring Boot (Examples)

### Redis / Valkey

```yaml
spring:
  data:
    redis:
      host: 10.0.0.5
      port: 6379
      password: ${sm://projects/PROJECT_ID/secrets/REDIS_AUTH_TOKEN/versions/latest}
```

```java
@Service
@RequiredArgsConstructor
public class CacheService {

    private final StringRedisTemplate redis;

    public void save(String key, String value) {
        redis.opsForValue().set(key, value, Duration.ofMinutes(60));
    }
}
```

> **Note:** Memorystore Redis AUTH tokens are generated by GCP and only displayed once at creation. Secure them in **Secret Manager**.

### Memcached

```java
@Configuration
public class MemcachedConfig {

    @Bean
    public MemcachedClient memcachedClient() throws Exception {
        return new MemcachedClient(new InetSocketAddress("10.0.0.6", 11211));
    }
}
```
