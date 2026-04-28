```
graph TD
    subgraph On_Prem_Network [On-Premises Datacenter]
        On_Prem_Client[On-Prem Client<br>Initiator]
        On_Prem_DNS[On-Prem DNS Server<br>10.50.0.10]
    end

    subgraph Hybrid_Connection [Hybrid Connectivity<br>VPN/Interconnect]
        Connection_Pipe[Encrypted Tunnel]
    end

    subgraph VPC_Network [Google Cloud VPC Network]
        Inbound_Forwarding_Policy_IP["Inbound Forwarding entry point<br>(e.g., 10.128.0.5)"]
        Metadata_Server["VPC Metadata Server<br>(35.199.191.8)"]
        GCP_Resource[GCP VM / Resource<br>_db.app.internal_]
    end

    subgraph Cloud_DNS_Infrastructure [Cloud DNS Infrastructure]
        Private_Zone_DB["Cloud DNS Private Zone<br>_app.internal_"]
    end

    %% Workflow: On-Prem resolves name INSIDE GCP via Inbound Forwarding
    On_Prem_Client -- "1. Query: _db.app.internal_" --> On_Prem_DNS
    On_Prem_DNS -- "2. Match conditional forwarder<br>target: 10.128.0.5" --> Connection_Pipe
    Connection_Pipe -- "3. Forwarded Query" --> Inbound_Forwarding_Policy_IP
    Inbound_Forwarding_Policy_IP -- "4. Internal lookup" --> Metadata_Server
    Metadata_Server -- "5. Match Found in Private Zone" --> Private_Zone_DB
    Private_Zone_DB -. "6. A Record Answer" .-> Metadata_Server
    Metadata_Server -. "7. Answer relayed" .-> Inbound_Forwarding_Policy_IP
    Inbound_Forwarding_Policy_IP -. "8. Answer to tunnel" .-> Connection_Pipe
    Connection_Pipe -. "9. Answer to On-Prem DNS" .-> On_Prem_DNS
    On_Prem_DNS -. "10. Final Answer" .-> On_Prem_Client

    %% Optional connection indicating communication after DNS success
    On_Prem_Client -. "11. Connect to GCP Resource (10.128.x.x)" .-> GCP_Resource

    %% Styling
    style On_Prem_Client fill:#fff9c4,stroke:#fbc02d
    style On_Prem_DNS fill:#fff9c4,stroke:#fbc02d
    style Inbound_Forwarding_Policy_IP fill:#f8bbd0,stroke:#880e4f
    style Private_Zone_DB fill:#e1f5fe,stroke:#01579b
    style GCP_Resource fill:#e1f5fe,stroke:#01579b
    style Connection_Pipe stroke-width:4px,stroke-dasharray: 5 5,stroke:#757575
```