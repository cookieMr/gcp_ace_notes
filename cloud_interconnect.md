# Cloud Interconnect: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. Cloud Interconnect Overview

Cloud Interconnect provides a direct, physical connection between your on-premises network and Google's network. It is designed for high-bandwidth, mission-critical workloads.

- **Key Characteristics:**
  - **Bypasses the Internet:** Unlike Cloud VPN, traffic does not travel over the public internet.
  - **Predictable Performance:** Provides consistent latency and higher throughput.
  - **High Bandwidth:** Options for 10 Gbps or 100 Gbps circuits (Dedicated) or smaller increments (Partner).
  - **Security:** High security due to physical isolation. MACsec encryption is available for Dedicated Interconnect to secure data in transit.
  - **Gemini for Network Topology:** Use Gemini to optimize network routes, troubleshoot BGP flaps, and analyze latency across your Interconnect.

## 2. Interconnect Types (The Most Important Exam Distinction)

GCP offers several primary types: Dedicated, Partner, and Cross-Cloud.

### Dedicated Interconnect

- **Definition:** A physical connection between your on-premises network and a Google peering location.
- **Requirements:** You must physically meet Google at a colocation facility (Interconnect location).
- **Bandwidth:** 10 Gbps or 100 Gbps circuits.
- **SLA:** 99.9% or 99.99% depending on the configuration.
- **Best For:** High data volume, consistent traffic, and organizations with presence in colocation facilities.

### Partner Interconnect

- **Definition:** Connection provided through a third-party service provider (Partner) who already has a physical link to Google.
- **Requirements:** You connect to the Partner, and the Partner connects to Google.
- **Bandwidth:** Flexible options from 50 Mbps up to 10 Gbps or 50 Gbps.
- **SLA:** 99.9% or 99.99% depending on the configuration.
- **Best For:** Organizations that cannot physically meet Google at a colocation facility or need lower bandwidth than 10 Gbps.

### Cross-Cloud Interconnect

- **Definition:** A direct, high-speed connection between Google Cloud and other cloud providers (e.g., AWS, Azure).
- **Requirements:** No physical hardware setup by the user; Google manages the link to the other cloud provider.
- **Best For:** Multi-cloud architectures requiring low-latency connectivity between different cloud platforms.

## 3. Deployment Components

To set up Interconnect, you need:

1. VLAN Attachment: A logical connection between your Interconnect and your VPC.
2. Cloud Router: Required to manage BGP sessions and dynamic routing.
3. Physical Link: (For Dedicated) The fiber optic cable connecting your equipment to Google's.
4. Partner Connection: (For Partner) The logical circuit provided by the third-party partner.

## 4. High Availability (SLA)

To achieve the 99.99% SLA, you must follow specific architecture guidelines:

- **99.99% SLA:** Requires at least four VLAN attachments across two different Interconnect locations and two different Cloud Routers.
- **99.9% SLA:** Requires at least two VLAN attachments across two different Cloud Routers in a single Interconnect location.

## 5. Direct Peering vs. Carrier Peering (The "Other" Connections)

- **Direct Peering:** Used to reach *Google Workspace and YouTube* (not VPC resources). It connects directly to Google via BGP but is not part of the VPC.
- **Carrier Peering:** Similar to Direct Peering but provided via a partner. Used to reach Google Workspace.

## 6. Essential gcloud Commands

- **Create VLAN Attachment (Dedicated):**
  `gcloud compute interconnects attachments dedicated create [NAME] --interconnect=[INTERCONNECT_NAME] --router=[ROUTER_NAME] --region=[REGION] --vlan=[VLAN_ID]`
- **Create VLAN Attachment (Partner):**
  `gcloud compute interconnects attachments partner create [NAME] --router=[ROUTER_NAME] --region=[REGION] --edge-availability-domain=[DOMAIN]`
- **Describe Interconnect:**
  `gcloud compute interconnects describe [NAME]`

## 7. Exam Tips & Comparison

- **Interconnect vs. Cloud VPN:**
  - Choose Interconnect for reliability, high bandwidth, and data-heavy migrations.
  - Choose VPN for low cost, fast setup, and traffic that is acceptable over the public internet.
- **Encryption:** Remember that Cloud Interconnect does not encrypt traffic by default. If encryption is required, you must run HA VPN over Interconnect or enable MACsec (where supported).
- **BGP:** All Interconnect types require Cloud Router and Dynamic Routing (BGP).
- **VLAN Attachments:** These are the resources you actually "see" and manage in your project.

[Back to README](README.md)
