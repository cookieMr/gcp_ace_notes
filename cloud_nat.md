# Cloud NAT: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Cloud NAT Overview

Cloud NAT (Network Address Translation) is a managed Google Cloud service that allows VM instances without external (public) IP addresses to access the internet.

- **Primary Purpose:** To provide outbound internet connectivity for private VMs while preventing those VMs from being directly accessible from the public internet (inbound).
- **Managed Service:** It is a software-defined, distributed service. It is NOT a single gateway instance.
- **Relationship with Cloud Router:** Cloud NAT is a configuration that is applied to a **Cloud Router**.

## 2. Key Characteristics

- **Outbound-Only:** Cloud NAT allows outbound connections and return traffic. It does **not** allow unsolicited inbound connections.
- **Regional Scope:** A Cloud NAT gateway is a regional resource.
- **Static IP Support:** You can assign specific static external IP addresses to the Cloud NAT gateway to whitelist traffic.
- **Dynamic Port Allocation (2026 Update):** A more scalable feature that allows the NAT gateway to adjust the number of ports assigned to each VM based on its actual usage, reducing the risk of port exhaustion failures.

## 3. Supported Resources

Cloud NAT provides NAT services for resources without external IP addresses:

- **Compute Engine VMs:** Standard, N4, and C4 machine types in a VPC.
- **GKE Nodes and Pods:** Private cluster nodes use Cloud NAT for outbound access.
- **Serverless (Run/Functions/App Engine):** When using a Serverless VPC Access Connector or Direct VPC Egress.
- **Private Service Connect (PSC) (2026 Update):** Cloud NAT can now provide NAT services for traffic destined for Private Service Connect endpoints.

## 4. Architecture and Configuration

To set up Cloud NAT, you need:

1.  **VPC Network:** The network containing the private resources.
2.  **Cloud Router:** A regional router in the same region.
3.  **Cloud NAT Gateway:** Configured on the Cloud Router.

- **Mapping Options:**
  - **Primary IP ranges:** NAT for only the primary IP range of a subnet.
  - **Secondary IP ranges:** NAT for secondary ranges (e.g., GKE pods).
  - **All ranges:** NAT for all ranges in all subnets of the region.

## 5. Security and Logging

- **Cloud NAT Logging:** Enable logging to capture connection details, including source/destination IP addresses and ports.
- **Port Reservation:** By default, Cloud NAT reserves a fixed number of ports (64). Using **Dynamic Port Allocation** is recommended for better scalability.

## 6. Essential gcloud Commands

- **Create Cloud Router:** `gcloud compute routers create [ROUTER_NAME] --network=[VPC] --region=[REGION]`
- **Create Cloud NAT Gateway:** `gcloud compute routers nats create [NAT_NAME] --router=[ROUTER_NAME] --region=[REGION] --auto-allocate-nat-external-ips --nat-all-subnet-ip-ranges`
- **List NAT Gateways:** `gcloud compute routers nats list --router=[ROUTER_NAME] --region=[REGION]`

## 7. Exam Tips

- **Private Google Access vs. Cloud NAT:**
  - Use **Private Google Access** to reach Google APIs (GCS, BigQuery) without an external IP.
  - Use **Cloud NAT** to reach the general internet (e.g., package repositories) without an external IP.
- **High Availability:** Cloud NAT is automatically highly available within its region.
- **IAP SSH:** Remember that IAP TCP Forwarding allows you to SSH into a VM without an external IP, but the VM still needs Cloud NAT for internet-based updates.

[Back to README](README.md)
