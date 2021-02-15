---
title: Azure IoT Central 中的遥测、属性和命令负载 |Microsoft Docs
description: Azure IoT Central 设备模板使你可以指定设备的遥测、属性和命令必须实现。 了解设备可与 IoT Central 交换的数据的格式。
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 5a9f6fa79da59425e4972dddd21ffdea15af73e7
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127900"
---
# <a name="telemetry-property-and-command-payloads"></a>遥测、属性和命令有效负载

本文适用于设备开发人员。

Azure IoT Central 中的设备模板是用于定义以下内容的蓝图：

* 设备发送到 IoT Central 的遥测。
* 设备与 IoT Central 进行同步的属性。
* IoT Central 在设备上调用的命令。

本文介绍了对于设备开发人员而言，为设备模板中定义的遥测、属性和命令发送和接收的 JSON 有效负载。

本文不介绍每种可能的遥测、属性和命令有效负载类型，但这些示例演示了所有密钥类型。

每个示例都显示了一个来自设备模型的代码段，它定义了类型和示例 JSON 有效负载，以说明设备应该如何与 IoT Central 应用程序进行交互。

> [!NOTE]
> IoT Central 接受任何有效的 JSON，但它仅在与设备模型中的定义匹配时才可用于可视化。 可以导出与定义不匹配的数据，请参阅 [将 IoT 数据导出到 Azure 中的目标](howto-export-data.md)。

定义设备模型的 JSON 文件使用 [数字克隆定义语言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

有关显示使用中的部分负载的设备代码示例，请参阅 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md) 教程。

## <a name="view-raw-data"></a>查看原始数据

IoT Central 允许查看设备发送到应用程序的原始数据。 此视图可用于排查从设备发送的有效负载的问题。 查看设备正在发送的原始数据：

1. 从 " **设备** " 页面导航到该设备。

1. 选择 " **原始数据** " 选项卡：

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="“原始数据”视图":::

    在此视图中，你可以选择要显示的列，并设置要查看的时间范围。 “未建模数据”列显示设备中与设备模板中的任何属性或遥测定义不匹配的数据。

## <a name="telemetry"></a>遥测技术

### <a name="primitive-types"></a>基元类型

本部分说明设备流式传输到 IoT Central 应用程序的基元遥测类型的示例。

设备模型中的以下代码片段显示了 `boolean` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "BooleanTelemetry": true }
```

设备模型中的以下代码片段显示了 `string` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

设备模型中的以下代码片段显示了 `integer` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "IntegerTelemetry": 23 }
```

设备模型中的以下代码片段显示了 `double` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "DoubleTelemetry": 56.78 }
```

设备模型中的以下代码片段显示了 `dateTime` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

设备客户端应以 JSON 形式发送遥测数据，如以下示例- `DateTime` 类型必须是 ISO 8061 格式：

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

设备模型中的以下代码片段显示了 `duration` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

设备客户端应发送类似于以下示例的 JSON：持续时间必须为 ISO 8601 格式：

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>复杂类型

本部分说明设备流式传输到 IoT Central 应用程序的复杂遥测类型。

设备模型中的以下代码片段显示了 `geopoint` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** 架构类型不属于 [数字孪生定义语言规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 当前支持 **geopoint** 架构类型和 **location** 语义类型，以便向后兼容。

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 IoT Central 将值显示为地图上的 pin：

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

设备模型中的以下代码片段显示了 `Enum` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 可能的值为 `0` 、 `1` 和， `2` 它们在 IoT Central 中显示为、和 `Item1` `Item2` `Item3` ：

```json
{ "EnumTelemetry": 1 }
```

设备模型中的以下代码片段显示了 `Object` 遥测类型的定义。 此对象具有三个类型为 `dateTime` 、 `integer` 和的字段 `Enum` ：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 `DateTime` 类型必须符合 ISO 8061。 的可能值 `Property3` 为 `0` 、 `1` 和，它们在 IoT Central 中显示为 `Item1` 、和 `Item2` `Item3` ：

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

设备模型中的以下代码片段显示了 `vector` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>事件和状态类型

本部分演示设备发送到 IoT Central 应用程序的遥测事件和状态的示例。

设备模型中的以下代码片段显示事件类型的定义 `integer` ：

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

设备客户端应将事件数据作为 JSON 发送，如以下示例所示：

```json
{ "IntegerEvent": 74 }
```

设备模型中的以下代码片段显示了 `integer` 状态类型的定义：

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 状态。 可能的整数状态值为 `1` 、 `2` 或 `3` ：

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>属性

> [!NOTE]
> 属性的负载格式适用于在07/14/2020 或之后创建的应用程序。

### <a name="primitive-types"></a>基元类型

本部分演示设备发送到 IoT Central 应用程序的基元属性类型的示例。

设备模型中的以下代码片段显示了 `boolean` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "BooleanProperty": false }
```

设备模型中的以下代码片段显示了 `boolean` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "LongProperty": 439 }
```

设备模型中的以下代码片段显示了 `date` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性。 `Date` 类型必须符合 ISO 8061：

```json
{ "DateProperty": "2020-05-17" }
```

设备模型中的以下代码片段显示了 `duration` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，此有效负载类似于以下示例，因为设备克隆期间的报告属性必须符合 ISO 8601 持续时间：

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

设备模型中的以下代码片段显示了 `float` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "FloatProperty": 1.9 }
```

设备模型中的以下代码片段显示了 `string` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>复杂类型

本部分演示设备发送到 IoT Central 应用程序的复杂属性类型的示例。

设备模型中的以下代码片段显示了 `geopoint` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> **Geopoint** 架构类型不属于 [数字孪生定义语言规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 当前支持 **geopoint** 架构类型和 **location** 语义类型，以便向后兼容。

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

设备模型中的以下代码片段显示了 `Enum` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性。 可能的值为 `0` 、 `1` 和，它们在 IoT Central 中显示为 `Item1` 、和 `Item2` `Item3` ：

```json
{ "EnumProperty": 1 }
```

设备模型中的以下代码片段显示了 `Object` 属性类型的定义。 此对象包含两个具有类型 `string` 和的字段 `integer` ：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

设备模型中的以下代码片段显示了 `vector` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>可写的属性类型

本部分介绍设备从 IoT Central 应用程序接收的可写属性类型的示例。

IoT Central 需要从设备到可写属性更新的响应。 响应消息应包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段。

`ac` 使用下表中的值的数值字段：

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202`  或 `'ac': 201` | 挂起的 | 属性更改操作已挂起或正在进行 |
| `'ac': 4xx` | 错误 | 请求的属性更改无效或出现错误 |
| `'ac': 5xx` | 错误 | 设备在处理请求的更改时遇到意外错误。 |

`av` 发送到设备的版本号。

`ad` 是一个选项字符串说明。

设备模型中的以下代码片段显示可写 `string` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

在处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括从 IoT Central 收到的原始更新的版本号。 IoT Central 收到此消息时，它会将属性标记为在 UI 中 **同步** ：

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

设备模型中的以下代码片段显示可写 `Enum` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

在处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括从 IoT Central 收到的原始更新的版本号。 IoT Central 收到此消息时，它会将属性标记为在 UI 中 **同步** ：

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>命令

以下来自设备模型的代码段显示了没有参数并且不希望设备返回任何内容的命令的定义：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

设备在请求中接收空负载，并应在响应中返回空负载，并使用 `200` HTTP 响应代码来指示成功。

设备模型中的以下代码片段显示了具有 integer 参数并且需要设备返回整数值的命令的定义：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

设备接收一个整数值作为请求负载。 设备应返回整数值作为响应负载，并使用 `200` HTTP 响应代码来指示成功。

设备模型中的以下代码片段显示具有 object 参数并且需要设备返回对象的命令的定义。 在此示例中，两个对象都具有整数和字符串字段：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

以下代码片段显示了发送到设备的示例请求负载：

```json
{ "Field1": 56, "Field2": "A string value" }
```

以下代码片段显示了从设备发送的示例响应负载。 使用 `200` HTTP 响应代码来指示成功：

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>长时间运行的命令

设备模型中的以下代码片段显示了命令的定义。 命令有一个整数参数，并期望设备返回整数值：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

设备接收一个整数值作为请求负载。 如果设备需要时间来处理此命令，则它应返回带有 HTTP 响应代码的空响应有效负载， `202` 以指示设备已接受处理请求。

设备处理完请求后，应将属性发送到 IoT Central，如以下示例所示。 属性名称必须与命令名称相同：

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>脱机命令

在 IoT Central web UI 中，你可以选择 " **脱机时队列** " 选项。 脱机命令是从解决方案发送到设备的单向通知，该通知是在设备连接后立即传递的。 脱机命令可以有请求参数，但不返回响应。

如果从设备模板导出模型或接口，则不包括 **脱机** 设置。 您无法通过查看导出的模型或接口 JSON 来确定命令是脱机命令。

脱机命令使用 [IoT 中心云到设备的消息](../../iot-hub/iot-hub-devguide-messages-c2d.md) 将命令和负载发送到设备。

设备模型中的以下代码片段显示了命令的定义。 该命令具有一个具有 datetime 字段和枚举的对象参数：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 2,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

如果在上一个代码段中的命令的设备模板 UI 中启用了 " **脱机时排队** " 选项，则设备接收的消息包括以下属性：

| 属性名称 | 示例值 |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解设备模板，接下来是了解有关如何将设备注册到 [Azure IoT Central](./concepts-get-connected.md) 的详细信息，以了解有关如何使用 IoT Central 注册设备的详细信息，以及 IoT Central 如何保护设备连接。
