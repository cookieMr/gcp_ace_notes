# Load Balancing: ACE Exam Study Guide (2026)

## 1. Load Balancing Overview

Google Cloud Load Balancing is a fully managed, software-defined service. It is not instance-based, so you don't need to manage infrastructure or scale it manually.

- Key Characteristics:
  - External vs. Internal: Internet-facing or private within your VPC.
  - Global vs. Regional: Traffic distribution across multiple regions or a single region.
  - Traffic Type: Layer 7 (HTTP/S) vs. Layer 4 (TCP/UDP).

## 2. External Load Balancers

### Global External Application Load Balancer (HTTP/S)

- Layer: Layer 7 (HTTP, HTTPS, HTTP/2).
- Scope: Global. Distributes traffic to the closest available backend.
- Features: URL maps (path-based routing), SSL termination, Cloud Armor integration, and Cloud CDN support.
- Backends: MIGs/ZIGs, NEGs for GKE/Serverless.

### External Proxy Network Load Balancer (TCP/SSL)

- Layer: Layer 4 (TCP with SSL termination).
- Scope: Global (Regional version available).
- Use Case: Non-HTTP traffic that requires SSL termination or proxying.

### External Passthrough Network Load Balancer (TCP/UDP)

- Layer: Layer 4 (TCP, UDP, ICMP).
- Scope: Regional.
- Nature: Passthrough. Preserves the source IP address of the client.
- Use Case: Simple TCP/UDP traffic where low latency is critical.

## 3. Internal Load Balancers

### Internal Application Load Balancer (HTTP/S)

- Layer: Layer 7.
- Scope: Regional.
- Use Case: Microservices communication within a VPC requiring path-based routing.

### Internal Proxy Network Load Balancer (TCP)

- Layer: Layer 4.
- Scope: Regional.
- Use Case: Internal TCP traffic requiring proxying services.

### Internal Passthrough Network Load Balancer (TCP/UDP)

- Layer: Layer 4.
- Scope: Regional.
- Nature: Passthrough. Very low latency.
- Use Case: Database clusters, legacy applications inside the VPC.

## 4. Summary Table for the Exam

| Load Balancer Type              | Layer | Scope      | Traffic Type        | Proxy? |
| :------------------------------ | :---- | :--------- | :------------------ | :----- |
| **Global External App LB**      | L7    | Global     | HTTP, HTTPS, HTTP/2 | Yes    |
| **Regional External App LB**    | L7    | Regional   | HTTP, HTTPS         | Yes    |
| **External Proxy Network LB**   | L4    | Global/Reg | TCP, SSL            | Yes    |
| **External Passthrough Net LB** | L4    | Regional   | TCP, UDP            | No     |
| **Internal App LB**             | L7    | Regional   | HTTP, HTTPS         | Yes    |
| **Internal Passthrough Net LB** | L4    | Regional   | TCP, UDP            | No     |

## 5. Components of a Load Balancer

- Forwarding Rule: Directs traffic based on IP, protocol, and port.
- Target Proxy: Terminates the connection and forwards it to the URL map.
- URL Map: Defines path-based routing rules (e.g., /images vs /api).
- Backend Service: Manages health checks, session affinity, and backend pools.
- Health Check: Regularly polls backends to ensure they are healthy.

## 6. Gemini and Load Balancing

- Gemini for Network Intelligence: Use Gemini to analyze traffic patterns across your load balancers and detect anomalies or potential DDoS attacks.
- SSL Certificate Management: Leverage Gemini to receive alerts and automated renewal recommendations for SSL certificates managed by your load balancers.
- Troubleshooting: Use Gemini in the Cloud Console to troubleshoot backend latency and health check failures with natural language queries.

## 7. Essential gcloud Commands

- Create a health check: `gcloud compute health-checks create http [NAME] --port 80`
- Create a backend service: `gcloud compute backend-services create [NAME] --protocol=HTTP --health-checks=[HC_NAME] --global`
- Add backends to service: `gcloud compute backend-services add-backend [NAME] --instance-group=[GROUP_NAME] --global`
- Create a URL map: `gcloud compute url-maps create [MAP_NAME] --default-service=[BACKEND_NAME]`

## 8. Exam Tips

- Preserving Client IP: For L4 traffic, use the External Passthrough Network Load Balancer.
- Path-based Routing: Only Application Load Balancers (L7) support URL maps.
- SSL Termination: Proxy-based load balancers (App LB, Proxy Net LB) handle SSL at the load balancer level.
- Cloud Armor/CDN: These integrate only with the Global External Application Load Balancer.
- Session Affinity: Use if a client needs to stick to the same backend instance.
