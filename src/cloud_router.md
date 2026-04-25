# Cloud Router (GCP)

![Cloud Router](images/cloud_router.png)

_Image source: Google Cloud Documentation_

## 1. Overview

**Cloud Router** is a fully managed BGP routing service that dynamically exchanges routes between your Google Cloud VPC and on-premises networks. It eliminates the need for static routes by automatically discovering and advertising network paths.

> **BGP (Border Gateway Protocol)**: The core routing protocol of the internet that allows different networks to exchange reachability information. It uses TCP port 179 and maintains connections between autonomous systems to share routing tables and adapt to network changes automatically.

## 2. Key Features

| Feature                   | Description                                                      |
| ------------------------- | ---------------------------------------------------------------- |
| **BGP Protocol**          | Uses BGP MD5-authenticated sessions for secure route exchange    |
| **Dynamic Updates**       | Automatically learns new VPC subnets without manual intervention |
| **High Availability**     | Managed redundancy across zones within a region                  |
| **Custom Advertisements** | Control which routes are advertised with custom prefixes         |
| **Route Priority**        | Configurable route priorities for traffic engineering            |

## 3. When Cloud Router Is Required

Cloud Router is mandatory for:

1. **HA VPN** - Both classic and HA VPN implementations
2. **Dedicated Interconnect** - For dynamic routing to on-premises
3. **Partner Interconnect** - When using a service provider for hybrid connectivity

> **Exam Tip**: If you see "dynamic routing" in a hybrid connectivity question, Cloud Router is the answer.

## 4. Architecture

```
┌─────────────────┐         BGP Session         ┌─────────────────┐
│   On-Premises   │◄───────────────────────────►│   Cloud Router  │
│     Router      │    (Port 179 / TCP 179)     │   (GCP VPC)     │
└─────────────────┘                             └─────────────────┘
```

## 5. Configuration

### 5.1. Create a Cloud Router

```bash
gcloud compute routers create ROUTER_NAME \
    --region=us-central1 \
    --network=my-vpc-network \
    --asn=65001
```

### 5.2. Add BGP Interface and Peer

```bash
gcloud compute routers add-interface ROUTER_NAME \
    --interface=interface-0 \
    --ip-address=169.254.0.1 \
    --mask-length=30 \
    --region=us-central1

gcloud compute routers add-bgp-peer ROUTER_NAME \
    --peer-name=peer-1 \
    --interface=interface-0 \
    --peer-ip-address=169.254.0.2 \
    --peer-asn=65002 \
    --region=us-central1
```

### 5.3. Advertise Custom Prefixes

```bash
gcloud compute routers export-routes ROUTER_NAME --region=us-central1

gcloud compute routers update ROUTER_NAME \
    --advertisement-mode=CUSTOM \
    --region=us-central1

gcloud compute routers add-advertisement ROUTER_NAME \
    --advertised-ranges=10.0.0.0/16 \
    --region=us-central1
```

## 6. Route Advertisement Modes

| Mode        | Behavior                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------ |
| **DEFAULT** | Advertises all VPC subnets with [RFC 1918](https://datatracker.ietf.org/doc/html/rfc1918) ranges |
| **CUSTOM**  | Only advertises prefixes you explicitly configure                                                |

### 6.1. Default Behavior (DEFAULT Mode)

Automatically advertises:

- All connected subnets in the VPC
- Static routes you've configured
- Routes from Cloud NAT

### 6.2. Custom Advertising

Use when you need to:

- Advertise only specific subnets
- Advertise custom IP ranges (e.g., `172.16.0.0/12`)
- Filter what on-premises networks can reach

## 7. BGP Session Details

- **BGP Port**: TCP 179 (router must allow this)
- **Keepalive Interval**: 20 seconds (default)
- **Hold Timer**: 60 seconds (default)
- **MD5 Authentication**: Supported and recommended
- **Peer IP Addresses**: Use `169.254.0.0/30` link-local range

## 8. Common Scenarios

### 8.1. Scenario: On-Premises to GCP Communication

```
On-Prem Server → On-Prem Router → Cloud Router → VPC Subnet
                                    (BGP learns routes in both directions)
```

### 8.2. Scenario: HA VPN with Cloud Router

```
Cloud Router
    ├── BGP Session 1 ──► VPN Tunnel 1 (Zone A)
    └── BGP Session 2 ──► VPN Tunnel 2 (Zone B)
         (Automatic failover if one tunnel fails)
```

## 9. Important Exam Points

### 9.1. Do

- Use Cloud Router for **any** dynamic routing scenario
- Specify a **unique ASN** (64512-65534 for private, or your own)
  > **ASN (Autonomous System Number)**: A unique identifier assigned to an autonomous system (AS) for BGP routing. Private ASNs range from 64512-65534. Each network on both sides of a BGP peering must have an ASN to establish the session.
- Use the same ASN on both sides of a BGP session
- Remember Cloud Router **manages routes**, not traffic itself

### 9.2. Don't

- Use for VPC-to-VPC routing (use VPC Peering or TGW)
  > **TGW (Transit Gateway)**: A regional hub that connects VPCs and on-premises networks in a hub-and-spoke topology. Use it for simplifying multi-VPC architectures instead of managing multiple peerings.
- Use static routes when dynamic routing is required
- Forget to allow TCP 179 in firewall rules for on-prem router

## 10. Troubleshooting

| Issue                | Solution                                                 |
| -------------------- | -------------------------------------------------------- |
| Routes not exchanged | Check BGP session state; verify firewall allows port 179 |
| One direction only   | Check route advertisements match expected prefixes       |
| Flapping connection  | Verify BGP timers (hold-time should be ≥ 3x keepalive)   |

### 10.1. Verify BGP Status

```bash
gcloud compute routers get-status ROUTER_NAME \
    --region=us-central1 \
    --format="yaml(bgpSessions)"
```

## 11. Comparison with Alternatives

| Service           | Use Case                          | Routing Type  |
| ----------------- | --------------------------------- | ------------- |
| **Cloud Router**  | Hybrid cloud connectivity         | Dynamic (BGP) |
| **Static Routes** | Simple single-hops                | Manual        |
| **VPC Peering**   | VPC-to-VPC                        | No BGP needed |
| **Cloud NAT**     | Outbound internet for private VMs | No BGP        |

## 12. Exam Prep Summary

> **Key Takeaway**: Cloud Router = BGP + Dynamic Routing + Hybrid Connectivity. If a question mentions HA VPN, Interconnect, or dynamic route exchange with on-premises, Cloud Router is required.
