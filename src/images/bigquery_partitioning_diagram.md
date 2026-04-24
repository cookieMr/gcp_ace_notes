```
graph TD
    %% Define main logical concepts
    subgraph Conceptual View: Unpartitioned Table
        A[<b>Large Table <br/> 1 TB Total Data</b><br/>All dates mixed in single storage]
    end

    subgraph Logical View: Partitioned Table
        B[<b>Partitioned Table <br/> 1 TB Total Data</b><br/>Data organized into date segments]
        P1(Partition 2023-10-25 <br/>250 GB)
        P2(Partition 2023-10-26 <br/>250 GB)
        P3(Partition 2023-10-27 <br/>250 GB)
        P4(Partition 2023-10-28 <br/>250 GB)

        B -.-> P1 & P2 & P3 & P4
    end

    %% Define User Query action
    Query(<b>User Query:</b><br/>WHERE date = '2023-10-27')

    %% Scenario 1 Path
    Query -->|<b>Scenario 1:</b> No Partitioning<br/>Reads all data to find matching rows| A

    %% Scenario 2 Path
    Query -->|<b>Scenario 2:</b> Using Partitioning<br/>Queries the metadata first<br/>Picks only matching partition| P3
```
