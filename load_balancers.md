# GCP Load Balancing: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Load Balancing Overview

Google Cloud Load Balancing is a fully managed, software-defined service. It is not instance-based, so you don't need to manage infrastructure or scale it manually.

- **Key Characteristics:**
  - **External vs. Internal:** Determines if the load balancer is internet-facing or private within your VPC.
  - **Global vs. Regional:** Determines if the load balancer can distribute traffic across multiple regions or is confined to a single region.
  - **Traffic Type:** Layer 7 (HTTP/S) vs. Layer 4 (TCP/UDP).

## 2. External Load Balancers

These are used to distribute traffic from the Internet to your Google Cloud resources.

### Global External Application Load Balancer (HTTP/S)

- **Layer:** Layer 7 (HTTP, HTTPS, HTTP/2).
- **Scope:** Global. Distributes traffic to the closest available backend across multiple regions.
- **Features:** URL maps (path-based routing), SSL termination, Google Cloud Armor integration, and Cloud CDN support.
- **Backends:** Instance Groups (MIGs/ZIGs), Network Endpoint Groups (NEGs) for GKE/Serverless.

### External Proxy Network Load Balancer (TCP/SSL)

- **Layer:** Layer 4 (TCP with SSL termination support).
- **Scope:** Global (Regional version also available).
- **Use Case:** Non-HTTP traffic that requires SSL termination or proxying.

### External Passthrough Network Load Balancer (TCP/UDP)

- **Layer:** Layer 4 (TCP, UDP, ICMP).
- **Scope:** Regional.
- **Nature:** Passthrough (not a proxy). It preserves the source IP address of the client.
- **Use Case:** Simple TCP/UDP traffic where low latency is critical and proxying is not required.

## 3. Internal Load Balancers

These distribute traffic between resources _within_ your Google Cloud VPC or from on-premises via VPN/Interconnect.

### Internal Application Load Balancer (HTTP/S)

- **Layer:** Layer 7.
- **Scope:** Regional.
- **Use Case:** Microservices communication within a VPC requiring path-based routing.

### Internal Proxy Network Load Balancer (TCP)

- **Layer:** Layer 4.
- **Scope:** Regional.
- **Use Case:** Internal TCP traffic requiring proxying services.

### Internal Passthrough Network Load Balancer (TCP/UDP)

- **Layer:** Layer 4.
- **Scope:** Regional.
- **Nature:** Passthrough. Very low latency.
- **Use Case:** Database clusters (e.g., SQL Server Always On), legacy applications inside the VPC.

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

- **Forwarding Rule:** Directs traffic based on IP, protocol, and port to a target proxy or pool.
- **Target Proxy:** (For Proxy LBs) Terminates the connection and forwards it to the URL map or backend service.
- **URL Map:** (For App LBs) Defines path-based routing rules (e.g., `/images` goes to one backend, `/api` to another).
- **Backend Service:** Manages health checks, session affinity, and the backend pools (Instance Groups/NEGs).
- **Health Check:** Regularly polls backends to ensure they are healthy. Traffic is not sent to unhealthy backends.

## 6. Essential gcloud Commands

- **Create a health check:** `gcloud compute health-checks create http [NAME] --port 80`
- **Create a backend service:** `gcloud compute backend-services create [NAME] --protocol=HTTP --health-checks=[HC_NAME] --global`
- **Add backends to service:** `gcloud compute backend-services add-backend [NAME] --instance-group=[GROUP_NAME] --global`
- **Create a URL map:** `gcloud compute url-maps create [MAP_NAME] --default-service=[BACKEND_NAME]`

## 7. Exam Tips

- **Preserving Client IP:** If you need to see the original client IP at the instance level for L4 traffic, use the **External Passthrough Network Load Balancer**.
- **Path-based Routing:** Only **Application Load Balancers (L7)** support URL maps and path-based routing.
- **SSL Termination:** Proxy-based load balancers (App LB, Proxy Net LB) handle SSL certificates at the load balancer level, reducing load on your VMs.
- **Cloud Armor/CDN:** These services only integrate with the **Global External Application Load Balancer**.
- **Session Affinity:** Use this if a client needs to stick to the same backend instance during their session (e.g., for shopping carts).

[Back to root](./README.md)
