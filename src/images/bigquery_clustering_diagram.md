```
graph TD
    %% Define Query
    UserQuery(<b>User Query:</b><br/>SELECT * FROM sales<br/>WHERE CustomerID = 'C005')
    style UserQuery fill:#e0e0e0,color:black,stroke:#333

    %% Non-Clustered Scenario
    subgraph Scenario A: Non-Clustered Table
        DirectionA(No Order)

        NC_B1(Block 1<br/>IDs: C001, C099, C102)
        NC_B2(Block 2<br/>IDs: C100, C005, C500)
        NC_B3(Block 3<br/>IDs: C200, C001, C900)
        NC_B4(Block 4<br/>IDs: C010, C005, C002)

        DirectionA -.-> NC_B1 & NC_B2 & NC_B3 & NC_B4
    end

    %% Clustered Scenario
    subgraph Scenario B: Clustered Table by CustomerID
        DirectionB(Sorted Order)

        C_B1(Block 1<br/>IDs: <b>C001 - C010</b><br/>Min: C001, Max: C010)
        C_B2(Block 2<br/>IDs: C011 - C100)
        C_B3(Block 3<br/>IDs: C101 - C300)
        C_B4(Block 4<br/>IDs: C301 - C999)

        DirectionB -.-> C_B1 & C_B2 & C_B3 & C_B4
    end

    %% Connections showing Query path
    UserQuery ==>|Reads ALL Blocks| NC_B1
    UserQuery ==>|Reads ALL Blocks| NC_B2
    UserQuery ==>|Reads ALL Blocks| NC_B3
    UserQuery ==>|Reads ALL Blocks| NC_B4
    linkStyle 4,5,6,7 stroke-width:2px,stroke-dasharray: 5 5,stroke:red;

    UserQuery ===>|Consults Metadata<br/>Checks Ranges| DirectionB
    DirectionB ==>|<b>BLOCK PRUNING:</b> Reads Only Block 1| C_B1
    linkStyle 9 stroke-width:3px,stroke:green;
```
