# GCP Cloud NAT: ACE Exam Study Guide (2026)

[Back to root](./README.md)

## 1. Cloud NAT Overview

Cloud NAT (Network Address Translation) is a managed Google Cloud service that allows VM instances without external (public) IP addresses to access the internet.

- **Primary Purpose:** To provide outbound internet connectivity for private VMs while preventing those VMs from being directly accessible from the public internet (inbound).
- **Managed Service:** It is a software-defined, distributed, and managed service. It is NOT a proxy or a single gateway instance that can become a bottleneck.
- **Relationship with Cloud Router:** Cloud NAT is not a standalone resource; it is a configuration that is applied to a **Cloud Router**.

## 2. Key Characteristics

- **Outbound-Only:** Cloud NAT allows outbound connections and the established return traffic. It does **not** allow unsolicited inbound connections from the internet.
- **Regional Scope:** A Cloud NAT gateway is a regional resource. It can only provide NAT services for subnets in the same region.
- **Static IP Support:** You can assign a specific static external IP address (or multiple) to the Cloud NAT gateway, allowing you to whitelist your VPC's traffic in external firewalls.
- **Automatic IP Allocation:** Alternatively, you can let Google automatically allocate IP addresses for the NAT gateway based on demand.

## 3. Supported Resources

Cloud NAT provides NAT services for the following resources that do not have external IP addresses:

- **Compute Engine VMs:** Standard virtual machines in a VPC.
- **GKE Nodes and Pods:** Nodes in a private cluster use Cloud NAT for outbound access.
- **Cloud Run / Cloud Functions / App Engine:** When using a Serverless VPC Access Connector to route traffic through a VPC.

## 4. Architecture and Configuration

To set up Cloud NAT, you need:

1.  **VPC Network:** The network containing the private resources.
2.  **Cloud Router:** A regional router in the same region as the subnets needing NAT.
3.  **Cloud NAT Gateway:** Configured on the Cloud Router.

- **Mapping Options:**
  - **Primary IP ranges:** NAT for only the primary IP range of a subnet.
  - **Secondary IP ranges:** NAT for secondary ranges (e.g., GKE pods).
  - **All ranges:** NAT for all primary and secondary ranges in all subnets of the region.

## 5. Security and Logging

- **Cloud NAT Logging:** You can enable logging to capture information about NAT connections, including source and destination IP addresses, ports, and protocols.
- **Port Reservation:** Cloud NAT reserves a specific number of ports per VM instance (default is 64). If a VM exhausts its allocated ports, new connections will fail. This is a common troubleshooting topic.

## 6. Essential gcloud Commands

- **Create Cloud Router (Prerequisite):** `gcloud compute routers create [ROUTER_NAME] --network=[VPC] --region=[REGION]`
- **Create Cloud NAT Gateway:**
  `gcloud compute routers nats create [NAT_NAME] --router=[ROUTER_NAME] --region=[REGION] --auto-allocate-nat-external-ips --nat-all-subnet-ip-ranges`
- **List NAT Gateways:** `gcloud compute routers nats list --router=[ROUTER_NAME] --region=[REGION]`

## 7. Exam Tips

- **Private Google Access vs. Cloud NAT:**
  - Use **Private Google Access** to reach Google APIs (GCS, BigQuery) without an external IP.
  - Use **Cloud NAT** to reach the general internet (e.g., `apt-get update`) without an external IP.
- **High Availability:** Cloud NAT is automatically highly available within its region.
- **IAP SSH:** Remember that IAP TCP Forwarding allows you to SSH into a VM without an external IP, but the VM still needs Cloud NAT if it needs to download packages from the internet.
- **NAT Rules:** If you need to route traffic to different external IPs based on the destination, you can use NAT Rules (advanced feature).

[Back to root](./README.md)
