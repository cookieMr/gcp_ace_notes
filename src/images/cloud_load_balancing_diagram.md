A Mermaid diagram:
```
flowchart LR

    Client(("🌍 Users"))
        --> FR["<b>Forwarding Rule</b><br/>Static IP: 34.x.x.x<br/>Port: 443"]
        
    subgraph LB_Logic ["Global External Application Load Balancer"]
        FR --> Proxy["Target Proxy<br/>(HTTPS)"]
        Proxy --> LB["URL Map<br/>/pricing/* → Pricing Service<br/>/inventory/* → Inventory Service"]
    
        %% Note for the Forwarding Rule
        note2[("<b>NOTE</b>: Terminates the SSL/TLS connection")]
        note2 -.-> Proxy
    end

    %% Backend Services
    LB --> BE_Pricing["Backend Service: Pricing"]
    LB --> BE_Inventory["Backend Service: Inventory"]

    %% MIGs
    BE_Pricing --> MIG_Pricing["MIG: Pricing Service"]
    BE_Inventory --> MIG_Inventory["MIG: Inventory Service"]

    %% Instances
    MIG_Pricing --> PZ1["Pricing VM 1"]
    MIG_Pricing --> PZ2["Pricing VM 2"]
    MIG_Inventory --> IZ1["Inventory VM 1"]
    MIG_Inventory --> IZ2["Inventory VM 2"]
    MIG_Inventory --> IZ3["Inventory VM 3"]

    %% Note for the Forwarding Rule
    note1[("<b>NOTE: Forwarding Rule</b><br/>This is the 'entry point'.<br/>It routes traffic based on<br/>IP, protocol, and port to<br/>the Target Proxy.")]
    note1 -.-> FR

    style note1 fill:#fff5ad,stroke:#f5d142,stroke-width:2px
    style FR fill:#f1f3f4,stroke:#4285f4,stroke-width:2px
```
```
sequenceDiagram
    participant C as Client (Browser)
    participant LB as Load Balancer (TLS Termination)
    participant S as Backend Service (HTTP)

    C->>LB: HTTPS Request (Encrypted)
    LB->>C: TLS Handshake + Certificate
    LB->>LB: Decrypt TLS (Termination)
    LB->>S: HTTP Request (Plain)
    S->>LB: HTTP Response (Plain)
    LB->>C: HTTPS Response (Re‑encrypted)
```