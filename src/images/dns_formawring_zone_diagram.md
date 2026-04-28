```
graph TD
    subgraph VPC_Network [Google Cloud VPC Network]
        VM_Client[GCP VM Client]
        %% Wrapped text in quotes to handle parentheses and line breaks
        Metadata_Server["VPC Metadata Server<br>(35.199.191.8)"]
    end

    subgraph Cloud_DNS_Infrastructure [Cloud DNS Infrastructure]
        Private_Zone_DB["Cloud DNS Private Zone<br>_app.internal_"]
        Forwarding_Rule_DB["Cloud DNS Forwarding Zone<br>_corp.local_"]
    end

    subgraph Hybrid_Connection [Hybrid Connectivity VPN/Interconnect]
        Connection_Pipe[Encrypted Tunnel]
    end

    subgraph On_Prem_Network [On-Premises Datacenter]
        On_Prem_DNS["On-Prem DNS Server<br>(10.50.0.10)"]
        On_Prem_Resource["On-Prem Server<br>_dc1.corp.local_"]
    end

    %% CASE 1: Query Resolved by Cloud DNS
    VM_Client -- "1a. Query: _db.app.internal_" --> Metadata_Server
    Metadata_Server -- "2a. Match Found in Private Zone" --> Private_Zone_DB
    Private_Zone_DB -. "3a. A Record Answer" .-> Metadata_Server
    Metadata_Server -. "4a. Answer relayed" .-> VM_Client

    %% CASE 2: Query Resolved by On-Prem DNS
    VM_Client -- "1b. Query: _dc1.corp.local_" --> Metadata_Server
    Metadata_Server -- "2b. Match Found in Forwarding Zone" --> Forwarding_Rule_DB
    Forwarding_Rule_DB -- "3b. Forward to Target: 10.50.0.10" --> Connection_Pipe
    Connection_Pipe -- "4b. Relayed Query" --> On_Prem_DNS
    On_Prem_DNS -. "5b. Answer: dc1 is at 10.50.1.5" .-> Connection_Pipe
    Connection_Pipe -. "6b. Answer relayed" .-> Forwarding_Rule_DB
    Forwarding_Rule_DB -. "7b. Answer to Metadata Server" .-> Metadata_Server
    Metadata_Server -. "8b. Answer to VM" .-> VM_Client

    %% Styling
    style VM_Client fill:#e1f5fe,stroke:#01579b
    style On_Prem_DNS fill:#fff9c4,stroke:#fbc02d
    style Forwarding_Rule_DB fill:#f8bbd0,stroke:#880e4f
    style Connection_Pipe stroke-width:4px,stroke-dasharray: 5 5,stroke:#757575
```