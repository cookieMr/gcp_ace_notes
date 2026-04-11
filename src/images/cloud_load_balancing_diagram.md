A Mermaid diagram:
```
flowchart LR

    Client(("🌍 Users"))
        --> LB["Global External Application Load Balancer<br/><b>🔐 SSL Termination</b>"]

    LB --> URLMap["URL Map<br/>/pricing/* → Pricing Service<br/>/inventory/* → Inventory Service"]

    %% Backend Services
    URLMap --> BE_Pricing["Backend Service: Pricing"]
    URLMap --> BE_Inventory["Backend Service: Inventory"]

    %% MIGs
    BE_Pricing --> MIG_Pricing["MIG: Pricing Service"]
    BE_Inventory --> MIG_Inventory["MIG: Inventory Service"]

    %% Pricing VMs
    MIG_Pricing --> PZ1["Zone A - Pricing VM 1"]
    MIG_Pricing --> PZ2["Zone A - Pricing VM 2"]

    %% Inventory VMs
    MIG_Inventory --> IZ1["Zone B - Inventory VM 1"]
    MIG_Inventory --> IZ2["Zone B - Inventory VM 2"]
```
