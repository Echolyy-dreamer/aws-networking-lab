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
    subgraph AWS_VPC["AWS VPC"]
        direction TB
        AZA["AZA"] --- AWS_EC2_A["AWS-EC2-A"]
        AZB["AZB"] --- AWS_EC2_B["AWS-EC2-B"]
        RouterVPC((VPC Router))
        AWS_EC2_A --> RouterVPC
        AWS_EC2_B --> RouterVPC
    end

    subgraph AWS_Global_Network["AWS Global Network"]
        TGW["TGW (Transit Gateway)"]
        Accelerated_VPN_Endpoints["Accelerated VPN Endpoints"]
        subgraph VPN_Endpoint_1["VPN Endpoint 1"]
            direction LR
            VE1_1["Tunnel 1"]
            VE1_2["Tunnel 2"]
        end
        subgraph VPN_Endpoint_2["VPN Endpoint 2"]
            direction LR
            VE2_1["Tunnel 1"]
            VE2_2["Tunnel 2"]
        end
        TGW --> Accelerated_VPN_Endpoints
        Accelerated_VPN_Endpoints --> VPN_Endpoint_1
        Accelerated_VPN_Endpoints --> VPN_Endpoint_2
    end

    subgraph Public_Internet["Public Internet"]
        direction LR
        Lock1_1[/"IPsec Tunnel"/]
        Lock1_2[/"IPsec Tunnel"/]
        Lock2_1[/"IPsec Tunnel"/]
        Lock2_2[/"IPsec Tunnel"/]
    end

    subgraph ONPREM["ONPREM (On-Premises)"]
        Router1["Router1"]
        Router2["Router2"]
        SERVER1["SERVER1<br/>192.168.10.0/24"]
        SERVER2["SERVER2<br/>192.168.11.0/24"]
        Router1 --- SERVER1
        Router2 --- SERVER2
    end

    %% 核心连接关系
    RouterVPC --> TGW
    TGW -->|Routes Exchanged using BGP| Router1
    TGW -->|Routes Exchanged using BGP| Router2

    %% VPN 隧道连接
    VE1_1 --- Lock1_1 --- Router1
    VE1_2 --- Lock1_2 --- Router1
    VE2_1 --- Lock2_1 --- Router2
    VE2_2 --- Lock2_2 --- Router2

    %% 样式美化
    classDef vpcStyle fill:#cce5ff,stroke:#004085,stroke-width:2px
    classDef globalStyle fill:#ffe8cc,stroke:#cc7722,stroke-width:2px
    classDef internetStyle fill:#ffdddd,stroke:#b30000,stroke-width:2px
    classDef onpremStyle fill:#ffcccc,stroke:#990000,stroke-width:2px
    classDef tgwStyle fill:#6f42c1,color:white,stroke:#492b7c,stroke-width:2px
    classDef endpointStyle fill:#6610f2,color:white,stroke:#3f0a96,stroke-width:2px

    class AWS_VPC vpcStyle
    class AWS_Global_Network globalStyle
    class Public_Internet internetStyle
    class ONPREM onpremStyle
    class TGW tgwStyle
    class Accelerated_VPN_Endpoints endpointStyle
