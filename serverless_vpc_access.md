# Serverless VPC Access: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Overview

Serverless VPC Access allows your Google Cloud serverless services to communicate with resources in your VPC network using internal (private) IP addresses.

- **Supported Services (The "Serverless" side):**
  - Cloud Run (Services and Jobs)
  - Cloud Functions (1st and 2nd Gen)
  - App Engine (Standard Environment)
- **Target Resources (The "VPC" side):**
  - Compute Engine VMs
  - Cloud SQL (with private IP)
  - Memorystore (Redis/Memcached)
  - Internal Load Balancers
  - On-premises resources (via Cloud VPN or Cloud Interconnect)

## 2. Key Characteristics

- **Managed Connector:** It acts as a bridge between the serverless environment and your VPC.
- **Regional Resource:** A connector is created in a specific region and can only be used by serverless services in that same region.
- **Dedicated Subnet:** Each connector requires a dedicated `/28` subnet range that does not overlap with any existing ranges in your VPC.
- **Throughput:** You can scale the connector's throughput by specifying the minimum and maximum number of instances.
- **Machine Types:** In 2026, connectors can leverage newer machine types (like N4) for improved performance and cost-efficiency.

## 3. Architecture and Configuration

To set up Serverless VPC Access, you need:

1.  **VPC Network:** The network containing the private resources.
2.  **Connector:** A regional resource configured on the VPC.
3.  **Subnet Range:** A reserved `/28` CIDR block (e.g., `10.8.0.0/28`).

- **Egress Settings (Important for Exam):**
  - **Private ranges only:** Only traffic destined for internal IP ranges (RFC 1918) is routed through the connector. Internet traffic goes through the standard Cloud Run/Functions public gateway.
  - **All traffic:** All outbound traffic (including internet) is routed through the connector. This is required if you want your serverless service to have a **Static Outbound IP** (via Cloud NAT).

## 4. Connectivity to Shared VPC

Serverless VPC Access supports Shared VPC, but with specific requirements:

- The connector must be created in the **Host Project** (where the VPC exists).
- The serverless service in the **Service Project** then points to the connector in the Host Project.
- The Service Project Admin needs the `roles/vpcaccess.user` role on the connector.

## 5. Security and IAM

- **IAM Roles:**
  - `roles/vpcaccess.admin`: Full control over connectors.
  - `roles/vpcaccess.user`: Permission to use a connector (required for deploying a Cloud Run service that uses the connector).
  - `roles/vpcaccess.viewer`: View-only access.
- **Firewall Rules:** You must ensure that the VPC's firewall allows ingress traffic from the connector's `/28` subnet to the target resources (e.g., allow port 3306 for Cloud SQL).

## 6. Gemini and Troubleshooting

- **Gemini for Network Intelligence:** Use Gemini in the Cloud Console to troubleshoot connectivity issues between your serverless application and VPC resources.
- **Connectivity Tests:** Integrate with Network Intelligence Center to run automated tests from the connector to the destination IP/port.

## 7. Essential gcloud Commands

- **Create a Connector:**
  `gcloud compute vpc-access connectors create [NAME] --network=[VPC] --region=[REGION] --range=[CIDR_28]`
- **List Connectors:**
  `gcloud compute vpc-access connectors list --region=[REGION]`
- **Deploy Cloud Run with Connector:**
  `gcloud run deploy [SERVICE_NAME] --image [IMAGE] --vpc-connector [CONNECTOR_NAME]`

## 8. Exam Tips

- **The "When to use" question:** If you see "Cloud Run" + "Cloud SQL Private IP" or "Memorystore," the answer is almost always **Serverless VPC Access Connector**.
- **The Subnet Rule:** Remember it must be exactly a **`/28`** CIDR block and must not overlap with any existing subnets.
- **The Cost:** Connectors incur costs even when not in use because they are backed by small, underlying VM instances.
- **Static IP for Cloud Run:** To give Cloud Run a static IP for a third-party firewall, you MUST use a VPC Connector with "All Traffic" egress and a **Cloud NAT** gateway.

[Back to README](README.md)
