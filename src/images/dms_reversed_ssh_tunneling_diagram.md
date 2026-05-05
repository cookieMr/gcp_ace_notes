```
graph LR
    subgraph GCP ["Google Cloud Platform (GCP)"]
        direction TB
        GCP_VM["Public VPS (Middleman)<br/>IP: 35.x.x.x"]
        ListenPort["Listening on Port 8080"]
    end

    subgraph OnPrem ["On-Premises / Private Network"]
        direction TB
        TargetDB["Private Database<br/>(PostgreSQL) Port 5432"]
        SSHClient["SSH Client<br/>(Initiates Tunnel)"]
    end

    %% The Tunnel Establishment
    SSHClient -- "1. ssh -R 8080:localhost:5432" --> GCP_VM
    
    %% The Logical Flow
    GCP_VM -. "2. Reverse Tunnel Active" .-> TargetDB

    %% External Access
    Admin["External Admin"] -- "3. Connects to 35.x.x.x:8080" --> GCP_VM
    GCP_VM -- "4. Traffic routed back through tunnel" --> TargetDB

    %% Styling
    style GCP_VM fill:#e1f5fe,stroke:#01579b
    style TargetDB fill:#fff9c4,stroke:#fbc02d
    style SSHClient fill:#f8bbd0,stroke:#880e4f
```