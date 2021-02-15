---
title: 分析网络安全性 - 安全组视图 - Azure PowerShell
titleSuffix: Azure Network Watcher
description: 本文介绍如何使用 PowerShell 通过安全组视图分析虚拟机安全性。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960581"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>使用 PowerShell 通过安全组视图分析虚拟机安全性

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> 安全组视图 API 不再维护，很快就会被弃用。 请使用提供相同功能的[有效安全规则功能](./network-watcher-security-group-view-overview.md)。 

安全组视图返回已应用于虚拟机的已配置的有效网络安全规则。 此功能可用于审核和诊断已在 VM 上配置以确保正确允许或拒绝流量的网络安全组和规则。 在本文中，我们将说明如何使用 PowerShell 检索虚拟机的已配置的有效安全规则


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>准备阶段

在此方案中，运行 `Get-AzNetworkWatcherSecurityGroupView` cmdlet 检索安全规则信息。

此方案假定已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定虚拟机的已配置有效安全规则。

## <a name="retrieve-network-watcher"></a>检索网络观察程序

第一步是检索网络观察程序实例。 将此变量传递给 `Get-AzNetworkWatcherSecurityGroupView` cmdlet。

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>获取 VM

需要针对虚拟机运行 `Get-AzNetworkWatcherSecurityGroupView` cmdlet。 以下示例获取 VM 对象。

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>检索安全组视图

下一步是检索安全组视图结果。

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>查看结果

以下示例是返回的结果的缩短响应。 该结果显示虚拟机上所有已应用的有效安全规则，分为以下几组：**NetworkInterfaceSecurityRules**、**DefaultSecurityRules** 和 **EffectiveSecurityRules**。

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>后续步骤

请访问[使用网络观察程序审核网络安全组 (NSG)](network-watcher-nsg-auditing-powershell.md)，了解如何自动执行网络安全组的验证。