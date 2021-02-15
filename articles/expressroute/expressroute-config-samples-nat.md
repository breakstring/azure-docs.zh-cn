---
title: Azure ExpressRoute：路由器配置示例 - NAT
description: 本页提供 Cisco 和 Juniper 路由器的路由器配置示例。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012659"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>用于设置和管理 NAT 的路由器配置示例

本文提供了适用于在使用 ExpressRoute 的情况下，适用于 Cisco ASA 和刺柏 SRX 系列路由器的 NAT 配置示例。 这些路由器配置仅用作指导的示例，不能按原样使用。 你需要与供应商合作，以便为你的网络提供相应的配置。

> [!IMPORTANT]
> 本页中的示例仅供指导。 必须与供应商的销售/技术团队和网络团队合作，以便指定符合需要的适当配置。 对于本页中所列配置的相关问题，Microsoft 不提供支持。 有关支持问题，必须与设备供应商联系。
> 
> 

* 以下路由器配置示例适用于 Azure 公共与 Microsoft 对等互连。 不为 Azure 专用对等互连配置 NAT。 有关更多详细信息，请查看 [ExpressRoute 对等互连](expressroute-circuit-peerings.md)和 [ExpressRoute NAT 要求](expressroute-nat.md)。

* 必须使用独立的 NAT IP 池来连接到 Internet 和 ExpressRoute。 在 Internet 与 ExpressRoute 中使用相同的 NAT IP 池会导致非对称路由和连接断开。


## <a name="cisco-asa-firewalls"></a>Cisco ASA 防火墙
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>从客户网络到 Microsoft 的流量的 PAT 配置

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>从 Microsoft 到客户网络的流量的 PAT 配置

**接口和方向：**

源接口 (流量进入 ASA) ：目标接口内 (流量在该位置退出 ASA) ：外部

**配置：**

NAT 池：

```console
object network outbound-PAT
    host <NAT-IP>
```

目标服务器：

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

客户 IP 地址的对象组：

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

NAT 命令：

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Juniper SRX 系列路由器
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1.为群集创建冗余的以太网接口

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2.创建两个安全区域
* 内部网络的信任区域和面向外部网络的边缘路由器的非信任区域
* 向区域分配适当的接口
* 在接口上允许服务

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3.在区域之间创建安全策略

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4.配置 NAT 策略
* 创建两个 NAT 池。 一个用于通过 NAT 转换出站到 Microsoft 的流量，另一个用于从 Microsoft 发往客户的流量。
* 创建规则以通过 NAT 转换相应的流量

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5.配置 BGP 以朝每个方向播发选择的前缀
请参阅[路由配置示例](expressroute-config-samples-routing.md)页中的示例。

### <a name="6-create-policies"></a>6.创建策略

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [ExpressRoute 常见问题解答](expressroute-faqs.md)。

