---
title: Azure Service Fabric sfctl 网格部署
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于创建 Service Fabric 网格资源的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257277"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
创建 Service Fabric 网格资源。

## <a name="commands"></a>命令

|Command|说明|
| --- | --- |
| create | 创建 Service Fabric 网格资源的部署。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
创建 Service Fabric 网格资源的部署。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --input-yaml-files [必需] | 以逗号分隔的所有 yaml 文件的相对路径或绝对路径路径，或者包含 yaml 文件 (递归) 的目录的相对路径或绝对路径。 |
| --parameters | 指向 yaml 文件的相对路径或绝对路径，或包含需要重写的参数的 json 对象。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

### <a name="examples"></a>示例

通过覆盖 yaml 文件中提到的参数将所有资源整合并部署到群集中
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

通过覆盖 yaml 文件中提到的参数将某个目录中的所有资源整合并部署到群集中

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

通过重写作为 json 对象直接传递的参数，将目录中的所有资源合并并部署到群集中
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
