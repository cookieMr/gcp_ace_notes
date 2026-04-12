A Mermaid diagram:
```
flowchart TB

    subgraph VPC_A["VPC A (Project A)"]
        direction TB
        SubnetA1["Subnet A1<br/>10.1.0.0/24"]
        SubnetA2["Subnet A2<br/>10.1.1.0/24"]
        VMsA["VMs / GKE Nodes"]
    end

    AtoB["VPC Peering A ↔ B<br/>Private RFC1918 Routing"]

    subgraph VPC_B["VPC B (Project B)"]
        direction TB
        SubnetB1["Subnet B1<br/>10.2.0.0/24"]
        SubnetB2["Subnet B2<br/>10.2.1.0/24"]
        VMsB["VMs / GKE Nodes"]
    end

    BtoC["VPC Peering B ↔ C<br/>Private RFC1918 Routing"]

    subgraph VPC_C["VPC C (Project C)"]
        direction TB
        SubnetC1["Subnet C1<br/>10.3.0.0/24"]
        SubnetC2["Subnet C2<br/>10.3.1.0/24"]
        VMsC["VMs / GKE Nodes"]
    end

    VPC_A <--> AtoB <--> VPC_B <--> BtoC <--> VPC_C

    VPC_A -. "NO ROUTING<br/>A ↛ C<br/>(Non‑Transitive)" .- VPC_C
```
