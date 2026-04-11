A Mermaid diagram:
```
flowchart LR

    subgraph Clients["🌍 Global Clients"]
        C1["Client (Europe)"]
        C2["Client (USA)"]
        C3["Client (Asia)"]
    end

    %% Anycast IP
    C1 --> IP["Anycast IP<br/>203.0.113.10"]
    C2 --> IP
    C3 --> IP

    %% Global PoPs
    subgraph PoPs["Google Edge Locations Advertising the Same IP"]
        EU["🇪🇺 Europe PoP<br/>203.0.113.10"]
        US["🇺🇸 USA PoP<br/>203.0.113.10"]
        AS["🇯🇵 Asia PoP<br/>203.0.113.10"]
    end

    IP --> EU
    IP --> US
    IP --> AS

    %% DNS Servers
    EU --> DNS1["Cloud DNS Server"]
    US --> DNS2["Cloud DNS Server"]
    AS --> DNS3["Cloud DNS Server"]

```
