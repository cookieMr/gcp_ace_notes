```
sequenceDiagram
    participant Dev as Deployment System
    participant LB as Load Balancer
    participant V_A as Version A (Control)
    participant V_B as Version B (Variant)
    participant Mon as Analytics/Monitoring

    Dev->>LB: Deploy Version B alongside Version A
    LB-->>Dev: Both versions registered

    Dev->>LB: Configure traffic split (e.g., 50/50)
    LB->>V_A: Route portion of traffic
    LB->>V_B: Route portion of traffic

    V_A-->>Mon: Send metrics (CTR, conversions, errors)
    V_B-->>Mon: Send metrics (CTR, conversions, errors)

    Mon-->>Dev: Compare performance A vs B

    alt Version B Performs Worse
        Dev->>LB: Reduce or stop traffic to B
        LB->>V_A: Route majority or all traffic
        Dev-->>Dev: Mark B as failed experiment
    else Version B Performs Better
        Dev->>LB: Increase traffic to B
        LB->>V_B: Route majority or all traffic
        Dev-->>Dev: Promote B to new baseline
    end
```
