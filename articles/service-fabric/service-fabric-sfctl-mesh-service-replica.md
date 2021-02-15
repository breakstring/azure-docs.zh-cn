---
title: Azure Service Fabric sfctl 网格服务-副本
description: 了解 sfctl（Azure Service Fabric 命令行接口）。 包含用于获取应用程序资源的副本详细信息的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245715"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
获取副本详细信息并列出应用程序资源中给定服务的副本。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| list | 列出某个服务的所有副本。 |
| 显示 | 获取应用程序服务的给定副本。 |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
列出某个服务的所有副本。

获取某个服务的所有副本的相关信息。 此信息包括服务副本的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --service-name [必需] | 服务的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
获取应用程序服务的给定副本。

获取具有给定名称的服务副本的相关信息。 此信息包括服务副本的说明和其他属性。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --name -n [必需] | 服务副本的名称。 |
| --service-name [必需] | 服务的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
