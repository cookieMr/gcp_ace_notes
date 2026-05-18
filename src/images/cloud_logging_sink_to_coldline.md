```
graph TB
    subgraph "Source"
        A[Compute Engine VMs] -->|Logs| B(Google Cloud Logging)
    end

    subgraph "Processing"
        B -->|Log Sink Filter| C{Filter}
        C -->|Only Relevant Logs| D[Direct Export]
    end

    subgraph "Destination"
        D -->|Archive to Coldline| E[(Cloud Storage Bucket<br/>Class: COLDLINE)]
        E --> F[Long-term Retention<br/>& Compliance]
    end

    %% Simulated Note Node
    G["<b>Coldline Storage</b><br/>• Lowest cost for archives<br/>• Min 90-day retention<br/>• Rare access required"] -.-> E

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style E fill:#f96,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style D fill:#ff9,stroke:#333,stroke-width:4px
    
    style G fill:#fff,stroke:#333,stroke-width:1px,stroke-dasharray: 5 5,rx:5,ry:5,color:#333
    
    linkStyle 2 stroke:#ff9,stroke-width:4px;
    linkStyle 4 stroke:#999,stroke-width:1px,stroke-dasharray: 5 5;
```