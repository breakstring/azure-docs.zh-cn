---
title: Azure Service Fabric CLI- sfctl
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包括一系列命令和子组。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257047"
---
# <a name="sfctl"></a>sfctl
用于管理 Service Fabric 群集和实体的命令。 此版本与 Service Fabric 7.0 运行时兼容。

命令遵循名词谓词模式。 有关详细信息，请参阅子组。

## <a name="subgroups"></a>子组
|子组|说明|
| --- | --- |
| [应用程序](service-fabric-sfctl-application.md) | 创建、删除和管理应用程序及应用程序类型。 |
| [chaos](service-fabric-sfctl-chaos.md) | 启动、停止和报告混沌测试服务。 |
| [cluster](service-fabric-sfctl-cluster.md) | 选择、管理和操作 Service Fabric 群集。 |
| [compose](service-fabric-sfctl-compose.md) | 创建、删除和管理 Docker Compose 应用程序。 |
| [容器](service-fabric-sfctl-container.md) | 在群集节点上运行与容器相关的命令。 |
| [events](service-fabric-sfctl-events.md) | 从事件存储中检索事件（如果已安装 EventStore 服务）。 |
| [is](service-fabric-sfctl-is.md) | 查询并向基础结构服务发送命令。 |
| [mesh](service-fabric-sfctl-mesh.md) | 删除和管理 Service Fabric 网格应用程序。 |
| [node](service-fabric-sfctl-node.md) | 管理构成群集的节点。 |
| [partition](service-fabric-sfctl-partition.md) | 查询和管理任何服务的分区。 |
| [property](service-fabric-sfctl-property.md) | 在 Service Fabric 名称下存储和查询属性。 |
| [replica](service-fabric-sfctl-replica.md) | 管理属于服务分区的副本。 |
| [rpm](service-fabric-sfctl-rpm.md) | 查询并向修复管理器服务发送命令。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | 管理独立 Service Fabric 群集。 |
| [服务](service-fabric-sfctl-service.md) | 创建、删除和管理服务、服务类型与服务包。 |
| [设置](service-fabric-sfctl-settings.md) | 配置此 sfctl 实例的本地设置。 |
| [存储](service-fabric-sfctl-store.md) | 针对群集映像存储执行基本文件级别操作。 |

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
