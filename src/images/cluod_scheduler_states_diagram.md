```
graph TD
    %% Row 1: The Core States
    subgraph States [Job Lifecycle States]
        direction LR
        E[Enabled] <--> P[Paused]
        E <--> D[Disabled]
        P <--> D
    end

    %% Transition to Execution
    E -->|Scheduled Time Reached| Exec[Execution Triggered]

    %% Row 2: The Outcome/History
    subgraph History [Job History / Cloud Logging]
        direction LR
        Exec --> Success[Success]
        Exec --> Failure[Failure / Error Details]
    end

    %% Styling
    style States fill:none,stroke:#333,stroke-dasharray: 5 5
    style History fill:none,stroke:#333,stroke-dasharray: 5 5
    style E fill:#d4edda
    style D fill:#f8d7da
    style P fill:#fff3cd
```
