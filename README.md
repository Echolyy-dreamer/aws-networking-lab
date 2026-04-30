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
    %% 1. 【最上方】BGP 线路：强制置顶
    TGW -.->|Routes Exchanged using BGP| Router1

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
        
        subgraph EP1 ["VPN Endpoint"]
            direction LR
            EP1_1["1"]
            EP1_2["2"]
        end
        
        subgraph EP2 ["VPN Endpoint"]
            direction LR
            EP2_1["1"]
            EP2_2["2"]
        end
    end

    subgraph Public_Internet ["Public Internet"]
        direction TB
        IPSEC1[/"IPsec Tunnel"/]
        IPSEC2[/"IPsec Tunnel"/]
        IPSEC3[/"IPsec Tunnel"/]
        IPSEC4[/"IPsec Tunnel"/]
    end

    subgraph ONPREM ["ONPREM"]
        direction TB
        Router1["Router1"]
        Router2["Router2"]
        SERVER1["SERVER1\n192.168.10.0/24"]
        SERVER2["SERVER2\n192.168.11.0/24"]
        Router1 --- SERVER1
        Router2 --- SERVER2
    end

    %% 核心物理连接
    VPC_Router --> TGW
    TGW --> EP1
    TGW --> EP2

    %% 隧道映射
    EP1_1 -.-> IPSEC1 --> Router1
    EP1_2 -.-> IPSEC2 --> Router1
    EP2_1 -.-> IPSEC3 --> Router2
    EP2_2 -.-> IPSEC4 --> Router2

    %% 2. 【最下方】BGP 线路：强制置底
    TGW -.->|Routes Exchanged using BGP| Router2

    %% --- 样式方案 ---
    classDef vpc fill:#cce5ff,stroke:#004085,stroke-width:2px,color:#004085
    classDef global fill:#ffe8cc,stroke:#cc7722,stroke-width:2px,color:#cc7722
    classDef internet fill:#ffdddd,stroke:#b30000,stroke-width:2px,color:#b30000
    classDef onprem fill:#ffcccc,stroke:#990000,stroke-width:2px,color:#990000
    classDef tgw fill:#6f42c1,color:white,stroke:#492b7c,stroke-width:2px

    class AWS_VPC vpc
    class AWS_Global,EP1,EP2 global
    class Public_Internet internet
    class ONPREM onprem
    class TGW tgw
    
    %% BGP 连线改为深灰色加粗 (索引 0 和 11)
    linkStyle 0,11 stroke:#444,stroke-width:2px,stroke-dasharray: 5 5
