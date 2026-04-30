"# aws-networking-lab" 
Build a Dynamic, Highly-Available, Accelerated Site-to-Site VPN between AWS and a simulated on-premises network.

Involved: 
>AWS Site-to-Site VPN
>VPN over Accelerated Connections (Global Accelerator / AWS Transit Gateway / Accelerated VPN)
>Customer Gateway + Virtual Private Gateway
>Dynamic routing (BGP)
>EC2-based simulation of on-prem router using strongSwan and FRR


```mermaid
flowchart LR
    subgraph AWS_VPC ["AWS VPC"]
        direction TB
        subgraph AZA ["AZA"]
            EC2A["AWS-EC2-A"]
        end
        subgraph AZB ["AZB"]
            EC2B["AWS-EC2-B"]
        end
        VPC_Router((VPC Router))
        EC2A --> VPC_Router
        EC2B --> VPC_Router
    end

    subgraph AWS_Global ["AWS Global Network"]
        direction TB
        TGW["TGW (Transit Gateway)"]
        subgraph Accelerated_VPN_Endpoints ["Accelerated VPN Endpoints"]
            direction LR
            VPN1["VPN Endpoint 1"]
            VPN2["VPN Endpoint 2"]
        end
        subgraph VPN1_Tunnels [" "]
            T1["Tunnel 1"]
            T2["Tunnel 2"]
        end
        subgraph VPN2_Tunnels [" "]
            T3["Tunnel 1"]
            T4["Tunnel 2"]
        end
        TGW --> Accelerated_VPN_Endpoints
        Accelerated_VPN_Endpoints --> VPN1_Tunnels
        Accelerated_VPN_Endpoints --> VPN2_Tunnels
    end

    subgraph Public_Internet ["Public Internet"]
        direction LR
        IPSEC1[/"IPsec Tunnel"/]
        IPSEC2[/"IPsec Tunnel"/]
        IPSEC3[/"IPsec Tunnel"/]
        IPSEC4[/"IPsec Tunnel"/]
    end

    subgraph ONPREM ["ONPREM"]
        direction TB
        Router1["Router1"]
        Router2["Router2"]
        SERVER1["SERVER1<br/>192.168.10.0/24"]
        SERVER2["SERVER2<br/>192.168.11.0/24"]
        Router1 --- SERVER1
        Router2 --- SERVER2
    end

    %% 核心连接（横向主链路）
    VPC_Router --> TGW
    TGW -.->|Routes Exchanged using BGP| Router1
    TGW -.->|Routes Exchanged using BGP| Router2

    %% VPN 隧道连接
    T1 --- IPSEC1 --- Router1
    T2 --- IPSEC2 --- Router1
    T3 --- IPSEC3 --- Router2
    T4 --- IPSEC4 --- Router2

    %% 样式还原
    classDef vpc fill:#cce5ff,stroke:#004085,stroke-width:2px
    classDef global fill:#ffe8cc,stroke:#cc7722,stroke-width:2px
    classDef internet fill:#ffdddd,stroke:#b30000,stroke-width:2px
    classDef onprem fill:#ffcccc,stroke:#990000,stroke-width:2px
    classDef tgw fill:#6f42c1,color:white,stroke:#492b7c,stroke-width:2px

    class AWS_VPC vpc
    class AWS_Global global
    class Public_Internet internet
    class ONPREM onprem
    class TGW tgw
