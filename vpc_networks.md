# GCP VPC Networks: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. VPC Fundamentals

A Virtual Private Cloud (VPC) is a global resource that provides networking functionality to Compute Engine, GKE, and App Engine.

- **Global Scope:** A single VPC can span multiple regions across the globe without needing to traverse the public internet.
- **VPC Types:**
  - **Auto Mode:** Automatically creates one subnet in each Google Cloud region. Uses a predefined IP range (`10.128.0.0/9`). (Not recommended for production).
  - **Custom Mode:** You manually create and define subnets and their IP ranges. This is the best practice for production environments.
- **Project Relationship:** By default, a project starts with a VPC named `default` (Auto mode).

## 2. Subnets (Regional)

While a VPC is global, subnets are **regional** resources.

- **Regional Isolation:** A subnet exists only within one region (e.g., `us-central1`).
- **IP Ranges:** Subnet ranges must not overlap within the same VPC.
- **Expansion:** You can expand the CIDR range of a subnet without downtime, but you **cannot shrink** it.
- **Secondary Ranges:** Used for GKE (alias IPs) to provide IP addresses for pods and services.
- **Private Google Access:** Allows VMs with only internal IP addresses to reach Google API services (GCS, BigQuery) without needing an external IP.

## 3. Routes

Routes define the paths that network traffic takes from a VM instance to other destinations.

- **System-Generated Routes:**
  - **Default Route:** Routes all traffic (`0.0.0.0/0`) to the Internet Gateway.
  - **Subnet Routes:** Automatically created for each subnet to allow communication between instances within the same VPC.
- **Static Routes:** Manually created to route traffic to specific destinations (e.g., a VPN gateway or a specific VM acting as a NAT).
- **Priority:** Routes are evaluated based on the **Longest Prefix Match** (most specific CIDR). If prefixes are identical, the route with the **Lowest Priority** number wins.

## 4. Firewall Rules

Firewall rules allow you to control which packets are allowed to or from your VM instances.

- **Implicit Rules (Cannot be deleted):**
  - **Allow Egress:** All outbound traffic is allowed by default.
  - **Deny Ingress:** All inbound traffic is blocked by default.
- **Rule Components:**
  - **Direction:** Ingress (Inbound) or Egress (Outbound).
  - **Action:** Allow or Deny.
  - **Priority:** 0 (Highest) to 65535 (Lowest). Default rules have priority 65534.
  - **Targets:** Defines which VMs the rule applies to (using **Network Tags**, Service Accounts, or "All instances").
  - **Sources/Destinations:** IP ranges, Network Tags, or Service Accounts.
- **Stateful Nature:** Firewall rules are **stateful**. If a connection is allowed, return traffic is automatically permitted.

## 5. VPC Network Peering & Shared VPC

- **VPC Network Peering:** Connects two VPC networks (even across different organizations) to allow internal IP communication. Traffic stays on Google's private backbone. No overlapping IP ranges allowed.
- **Shared VPC:** Allows an organization to connect resources from multiple projects to a common VPC network.
  - **Host Project:** Contains the Shared VPC network.
  - **Service Projects:** Attach their resources (VMs, GKE) to the Host Project's subnets.

## 6. Connectivity Services

- **Cloud VPN:** Connects your on-premises network to your VPC via an IPsec VPN tunnel over the public internet.
- **Cloud Interconnect:** Provides a direct, physical connection between your on-premises network and Google's network (Dedicated or Partner).
- **Cloud NAT:** Allows VMs without external IPs to access the internet for updates/downloads without exposing them to inbound connections.
- **Cloud Router:** Uses BGP to dynamically exchange routes between your VPC and your on-premises network.

## 7. Essential gcloud Commands

- **Create VPC:** `gcloud compute networks create [NAME] --subnet-mode=custom`
- **Create Subnet:** `gcloud compute networks subnets create [NAME] --network=[VPC] --region=[REGION] --range=[CIDR]`
- **Create Firewall Rule:** `gcloud compute firewall-rules create [NAME] --network=[VPC] --allow tcp:80 --target-tags=http-server`
- **List Routes:** `gcloud compute routes list`

## 8. Exam Tips

- **Global vs. Regional:** VPC is Global, Subnets are Regional, Firewall Rules are Global.
- **Conflict Resolution:** Longest Prefix Match always wins in routing.
- **IAP for SSH:** Remember the range `35.235.240.0/20` must be allowed for IAP TCP forwarding.
- **Networking Costs:** Egress traffic usually incurs costs; Ingress is usually free. Traffic within the same Zone is usually free; traffic between Zones in the same Region has a cost.

[Back to README](README.md)
