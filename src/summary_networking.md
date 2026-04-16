# Networking

![Networking](images/Networking-512-color.png)

_Image source: Google Cloud Documentation_

- [Google Cloud networking Overview - The Cloud Girl](https://www.thecloudgirl.dev/networking/networking-overview)

### **[VPC Networks](./vpc_networks.md)**

Global, virtual network for GCP resources. Provides isolation, subnet segmentation, firewall rules, and private communication between resources across regions and zones.

### **[VPC Peering](./vpc_peering.md)**

Private connection between two VPC networks in the same or different projects. No transit traffic; simpler than Shared VPC but less flexible for multi-org scenarios.

### **[Cloud NAT](./cloud_nat.md)**

Managed NAT service for VMs without external IPs to access the internet. Handles SNAT/DNAT, allows outbound-only internet access without exposing instances to inbound traffic.

### **[Cloud VPN](./cloud_vpn.md)**

Secure IPsec VPN tunnel between your VPC and on-premises network over the public internet. Uses Cloud Router for dynamic route exchange via BGP.

### **[Cloud Router](./cloud_router.md)**

Managed network router that enables dynamic routing (BGP) between your VPC and external networks. Automatically exchanges routes when network topology changes.

### **[Cloud Interconnect](./cloud_interconnect.md)**

Dedicated physical connection between your on-premises network and GCP without traversing the public internet. Higher bandwidth, lower latency than VPN. Includes Dedicated and Partner options.

### **[Load Balancers](./load_balancers.md)**

Globally distributed, software-defined load balancing for HTTP(S), TCP, UDP traffic. Distributes load across backend instances, supports health checks, SSL termination, and auto-scaling.

### **[Cloud CDN](./cloud_cdn.md)**

Content delivery network that caches content at Google's globally distributed edge locations. Reduces latency, offloads origin traffic, and supports cache invalidation.

### **[Cloud DNS](./cloud_dns.md)**

Scalable, reliable, managed authoritative DNS service. Provides low-latency DNS resolution with 100% SLA, supporting millions of domains with anycast routing.

### **[Serverless VPC Access](./serverless_vpc_access.md)**

Allows Cloud Run, Cloud Functions, and App Engine to connect to VPC resources using private IPs. Uses a managed connector or Direct VPC Egress for serverless-to-VPC communication.
