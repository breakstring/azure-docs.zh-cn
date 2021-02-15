---
title: 使用部署清单来部署模块和路由 - Azure IoT Edge
description: 了解部署清单如何声明要部署的模块、如何部署这些模块以及如何在它们之间创建消息路由。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f6c12b892e01aafd5beecdff14751481cf7fc96
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963391"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>了解如何在 IoT Edge 中部署模块和建立路由

每个 IoT Edge 设备至少运行两个模块：$edgeAgent 和 $edgeHub，它们构成了 IoT Edge 运行时。 IoT Edge 设备可以为任意数量的进程运行多个附加模块。 使用部署清单告知设备要安装哪些模块以及如何将它们配置为协同工作。

部署清单是一个 JSON 文档，用于描述以下内容：

* **IoT Edge 代理**模块孪生，其中包括三个组件：
  * 在设备上运行的每个模块的容器映像。
  * 用于访问包含模块映像的专用容器注册表的凭据。
  * 有关如何创建和管理每个模块的说明。
* **IoT Edge 中心**模块孪生：描述消息如何在模块之间流动，并最终传送到 IoT 中心。
* 任何附加模块孪生的所需属性（可选）。

必须使用部署清单配置所有 IoT Edge 设备。 在使用有效清单进行配置前，新安装的 IoT Edge 运行时会报告错误代码。

在 Azure IoT Edge 教程中，你将通过 Azure IoT Edge 门户中的向导生成部署清单。 此外，也可以使用 REST 或 IoT 中心服务 SDK 以编程方式应用部署清单。 有关详细信息，请参阅[了解 IoT Edge 部署](module-deployment-monitoring.md)。

## <a name="create-a-deployment-manifest"></a>创建部署清单。

从较高层面讲，部署清单是配置了所需属性的模块孪生的列表。 部署清单告知某个 IoT Edge 设备（或一组设备）要安装哪些模块，以及如何配置这些模块。 部署清单包含每个模块孪生的所需属性。 IoT Edge 设备将报告每个模块的报告属性。

每个部署清单中需要两个模块：`$edgeAgent` 和 `$edgeHub`。 这些模块属于管理 IoT Edge 设备及其上运行的模块的 IoT Edge 运行时。 有关这些模块的详细信息，请参阅[了解 IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

除了这两个运行时模块外，还可以添加多达50个要在 IoT Edge 设备上运行的模块。

仅包含 IoT Edge 运行时（edgeAgent 和 edgeHub）的部署清单是有效的。

部署清单遵循以下结构：

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>配置模块

定义 IoT Edge 运行时如何在部署中安装模块。 IoT Edge 代理是管理 IoT Edge 设备的安装、更新和状态报告的运行时组件。 因此，$edgeAgent 模块孪生包含所有模块的配置和管理信息。 此信息包括 IoT Edge 代理本身的配置参数。

$edgeAgent 属性遵循此结构：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 代理架构版本1.1 随 IoT Edge 版本1.0.10 一起发布，并启用模块启动顺序。 对于运行1.0.10 或更高版本的任何 IoT Edge 部署，建议使用架构1.1 版。

### <a name="module-configuration-and-management"></a>模块配置和管理

IoT Edge 代理所需的属性列表可用于定义要部署到 IoT Edge 设备的模块以及应如何配置和管理它们。

有关可以或必须包含的所需属性的完整列表，请参阅 [IoT Edge agent 和 IoT Edge 中心的属性](module-edgeagent-edgehub.md)。

例如：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

每个模块都有一个 **设置** 属性，其中包含模块 **映像**、容器注册表中容器映像的地址，以及用于在启动时配置映像的任何 **createOptions** 。 有关详细信息，请参阅 [如何为 IoT Edge 模块配置容器 create 选项](how-to-use-create-options.md)。

EdgeHub 模块和自定义模块还具有三个属性，这些属性告诉 IoT Edge agent 如何管理它们：

* **状态**：在第一次部署时，模块是应运行还是已停止。 必需。
* **RestartPolicy**：当和如果 IoT Edge 代理停止时应重新启动该模块。 必需。
* **StartupOrder**： *在版本1.0.10 中引入 IoT Edge。* 第一次部署时，IoT Edge 代理应启动模块的顺序。 此顺序是使用整数进行声明的，其中，将首先启动给定了启动值0的模块，然后再执行此操作。 EdgeAgent 模块没有启动值，因为它始终首先启动。 可选。

  IoT Edge 代理按启动值顺序启动模块，但不会等待每个模块完成启动，然后再转到下一个模块。

  如果某些模块依赖于其他模块，则启动顺序很有用。 例如，你可能想要先启动 edgeHub 模块，使其可以在其他模块启动时路由消息。 或者，您可能想要在将数据发送到的模块之前启动存储模块。 但是，您始终应设计模块来处理其他模块的故障。 它们是容器的性质，它们可能会随时停止和重新启动，以及任意数量的时间。

## <a name="declare-routes"></a>声明路由

IoT Edge 中心管理模块、IoT 中心与所有叶设备之间的通信。 因此，$edgeHub 模块孪生包含名为 *routes* 的所需属性，该属性声明如何在部署中传递消息。 可以在同一个部署中创建多个路由。

使用以下语法在 **$edgeHub** 所需属性中声明路由：

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 中心架构版本1.1 随 IoT Edge 版本1.0.10 一起发布，并启用路由优先级和生存时间。 对于运行1.0.10 或更高版本的任何 IoT Edge 部署，建议使用架构1.1 版。

每个路由都需要一个 *源* ，其中的消息来源于消息的 *接收* 位置。 *条件*是可选的，可以用来筛选消息。

你可以将 *优先级* 分配给你想要确保其消息先处理的路由。 此功能在以下情况下非常有用：上游连接较弱或受限，且你的关键数据应优先于标准遥测消息。

### <a name="source"></a>源

源指定消息来自何处。 IoT Edge 可以路由来自模块或叶设备的消息。

通过 IoT SDK，模块可以使用 ModuleClient 类为其消息声明特定的输出队列。 输出队列不是必需的，但对于管理多个路由很有帮助。 叶设备可以使用 IoT SDK 的 DeviceClient 类将消息发送到 IoT Edge 网关设备，就像它们将消息发送到 IoT 中心一样。 有关详细信息，请参阅[了解和使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。

源属性可采用以下任何值：

| 源 | 说明 |
| ------ | ----------- |
| `/*` | 所有设备到云的消息，或者来自任何模块或叶设备的孪生更改通知 |
| `/twinChangeNotifications` | 来自任何模块或叶设备的任何孪生更改（报告属性） |
| `/messages/*` | 由模块或叶设备通过某种输出或不通过任何输出发送的任何设备到云的消息 |
| `/messages/modules/*` | 由带部分输出或不带输出的模块发送的任何设备到云的消息 |
| `/messages/modules/<moduleId>/*` | 由特定模块通过某种输出或不通过任何输出发送的任何设备到云的消息 |
| `/messages/modules/<moduleId>/outputs/*` | 由特定模块通过某种输出发送的任何设备到云的消息 |
| `/messages/modules/<moduleId>/outputs/<output>` | 由特定模块通过特定输出发送的任何设备到云的消息 |

### <a name="condition"></a>条件

条件在路由声明中是可选的。 若要将所有消息从源传递到接收器，完全省略 WHERE 子句即可。 或者，可以使用 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-routing-query-syntax.md)来筛选满足条件的特定消息或消息类型。 IoT Edge 路由不支持基于孪生标记或属性筛选消息。

在 IoT Edge 中的模块之间传递的消息与在设备和 Azure IoT 中心之间传递的消息的格式是一样的。 所有消息都是 JSON 格式的，并具备 systemProperties、appProperties 和 body 参数  。

可使用以下语法围绕三个参数中的任何一个生成查询：

* 系统属性：`$<propertyName>` 或 `{$<propertyName>}`
* 应用程序属性：`<propertyName>`
* 正文属性：`$body.<propertyName>`

请参考[设备到云消息路由查询表达式](../iot-hub/iot-hub-devguide-routing-query-syntax.md)，查看针对消息属性创建查询的示例。

有一个 IoT Edge 特定的示例，即筛选从叶设备到网关设备的消息的情况。 来自模块的消息包含名为 **connectionModuleId** 的系统属性。 因此，若要将消息从叶设备直接路由到 IoT 中心，请使用以下路由来排除模块消息：

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>接收器

接收器定义消息发送到的位置。 只有模块和 IoT 中心可以接收消息。 无法将消息路由到其他设备。 接收器属性中没有通配符选项。

接收器属性可采用以下任何值：

| 接收器 | 说明 |
| ---- | ----------- |
| `$upstream` | 将消息发送到 IoT 中心 |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 将消息发送到特定模块的特定输入 |

IoT Edge 提供至少一次保证。 IoT Edge 中心在本地存储消息，以防路由无法将消息传送到其接收器。 例如，如果 IoT Edge 中心无法连接到 IoT 中心，或者目标模块未连接。

IoT Edge 中心会一直存储消息，直到达到在 [IoT Edge 中心所需属性](module-edgeagent-edgehub.md)的 `storeAndForwardConfiguration.timeToLiveSecs` 属性中指定的时间。

### <a name="priority-and-time-to-live"></a>优先级和生存时间

可以只使用定义路由的字符串或使用路由字符串、优先级整数和生存时间整数的对象来声明路由的情况。

选项 1：

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

选项2，在 IoT Edge 版本1.0.10 中引入 IoT Edge 中心架构版本1.1：

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**优先级** 值可以是0-9 （含），其中0是最高优先级。 消息根据其终结点排队。 针对特定终结点的所有优先级为0的消息将在处理针对同一终结点的任何优先级为1的消息之前进行处理，并向下处理。 如果同一终结点的多个路由具有相同的优先级，则其消息将按首先提供的方式进行处理。 如果未指定优先级，则会将路由分配给最低优先级。

除非显式设置，否则 **timeToLiveSecs** 属性将从 IoT Edge 集线器的 **storeAndForwardConfiguration** 继承其值。 该值可以是任何正整数。

有关如何管理优先级队列的详细信息，请参阅 [路由优先级和生存时间](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)的参考页。

## <a name="define-or-update-desired-properties"></a>定义或更新所需属性

部署清单指定部署到 IoT Edge 设备的每个模块的所需属性。 部署清单中的所需属性会覆盖模块孪生中当前存在的任何所需属性。

如果未在部署清单中指定模块孪生的所需属性，则 IoT 中心不会以任何方式修改模块孪生。 可以编程方式设置所需属性。

将使用用来修改设备孪生的相同机制来修改模块孪生。 有关详细信息，请参阅[模块孪生开发人员指南](../iot-hub/iot-hub-devguide-module-twins.md)。

## <a name="deployment-manifest-example"></a>部署清单示例

以下示例演示了有效部署清单文档的外观。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* 有关在 $edgeAgent 和 $edgeHub 中可以或必须包含的属性的完整列表，请参阅 [IoT Edge 代理和 IoT Edge 中心的属性](module-edgeagent-edgehub.md)。

* 至此，你已了解如何使用 IoT Edge 模块，接下来请继续[了解开发 IoT Edge 模块的要求和工具](module-development.md)。
