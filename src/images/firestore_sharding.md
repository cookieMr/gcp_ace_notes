A Mermaid diagram:
```
flowchart LR

    Client["🧑‍💻 Clients / Writers"]
        --> Dist["Shard Selector<br/>(Random or Hash-based)"]

    %% Shards
    Dist --> S1["Shard 1<br/>/counter/shard1"]
    Dist --> S2["Shard 2<br/>/counter/shard2"]
    Dist --> S3["Shard 3<br/>/counter/shard3"]
    Dist --> S4["Shard 4<br/>/counter/shard4"]

    %% Aggregation
    S1 --> Agg["Aggregator<br/>(Sum all shards)"]
    S2 --> Agg
    S3 --> Agg
    S4 --> Agg

    Agg --> Result["📊 Final Value<br/>(e.g., total counter)"]

```
