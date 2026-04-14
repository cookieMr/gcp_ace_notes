```
sequenceDiagram
    participant Dev as Deployment System
    participant LB as Load Balancer
    participant V1 as Stable Version
    participant V2 as Canary Version
    participant Mon as Monitoring System

    Dev->>LB: Deploy Canary (V2) alongside Stable (V1)
    LB-->>Dev: Canary registered

    Dev->>LB: Route small % of traffic to V2
    LB->>V1: Route majority of traffic
    LB->>V2: Route small canary traffic

    LB->>Mon: Send metrics (errors, latency, logs)
    Mon-->>Dev: Health evaluation

    alt Canary Unhealthy
        Dev->>LB: Rollback traffic to V1
        LB->>V1: 100% traffic to stable
        Dev-->>Dev: Canary removed
    else Canary Healthy
        Dev->>LB: Increase traffic to V2
        LB->>V2: Gradually more traffic
        LB->>Mon: Continue monitoring
    end

    Dev->>LB: Final shift to 100% V2
    LB->>V2: All traffic routed
    Dev-->>Dev: Decommission V1
```
