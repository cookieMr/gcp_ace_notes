A Mermaid diagram:
```
flowchart LR

    %% Clients
    PublicClient["User on the Internet"]
    PrivateClient["VM inside VPC"]

    %% DNS Query Flow
    PublicClient --> PubDNS["Public Cloud DNS Zone<br/>example.com<br/>A → 203.0.113.10"]
    PrivateClient --> PrivDNS["Private Cloud DNS Zone<br/>example.com<br/>A → 10.0.0.5"]

    %% Same Domain Name
    PubDNS --- SameName["Same Domain Name<br/>example.com"]
    PrivDNS --- SameName

    %% Results
    PubDNS --> PubResult["🌐 Public Answer<br/>203.0.113.10"]
    PrivDNS --> PrivResult["🔒 Private Answer<br/>10.0.0.5"]

```
