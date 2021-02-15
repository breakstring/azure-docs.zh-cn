---
title: 查询引入的遥测数据
description: 本文介绍如何查询引入遥测数据。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677488"
---
# <a name="query-ingested-telemetry-data"></a>查询引入的遥测数据

本文介绍如何从 Azure FarmBeats 查询引入传感器数据。

引入中的物联网数据 (IoT) 资源（如设备和传感器）是 FarmBeats 中的常见方案。 为设备和传感器创建元数据，然后将历史数据以规范格式引入 FarmBeats。 当传感器数据在 FarmBeats Datahub 上可用时，我们可以查询相同的来生成可操作的见解或生成模型。

## <a name="before-you-begin"></a>开始之前

在继续阅读本文之前，请确保已将 IoT 设备中的 FarmBeats 和引入传感器遥测数据安装到 FarmBeats。

若要引入传感器遥测数据，请访问引入 [历史遥测数据](ingest-historical-telemetry-data-in-azure-farmbeats.md)

在继续操作之前，还需要确保熟悉 FarmBeats REST Api，因为你将使用 Api 查询引入遥测。 有关 FarmBeats Api 的详细信息，请参阅 [FARMBEATS REST api](rest-api-in-azure-farmbeats.md)。 **确保能够向 FarmBeats Datahub 终结点发出 API 请求** 。

## <a name="query-ingested-sensor-telemetry-data"></a>查询引入传感器遥测数据

可以通过两种方法从 FarmBeats 访问和查询遥测数据：

- API 和
- 时序见解 (TSI) 。

### <a name="query-using-rest-api"></a>使用 REST API 查询

按照以下步骤使用 FarmBeats REST Api 查询引入传感器遥测数据：

1. 确定你感兴趣的传感器。 可以通过在/Sensor API 上发出 GET 请求来实现此目的。

> [!NOTE]
> 感兴趣的传感器对象的 **id** 和 **sensorModelId** 。

2. 如步骤1中所述，在 **sensorModelId** 的/SensorModel API 上创建 GET/{id}。 "传感器型号" 包含来自传感器的引入遥测的所有元数据和详细信息。 例如， **传感器型号** 对象中的 " **传感器度量值** " 包含有关传感器发送的度量值以及哪些类型和单位的详细信息。 例如，

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
记下传感器型号的 GET/{id} 调用的响应。

3. 使用以下输入有效负载在/Telemetry API 上执行 POST 调用

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. 来自/Telemetry API 的响应将如下所示：

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
在上面的示例响应中，查询的传感器遥测提供两个时间戳的数据和度量值名称 ( "moist_soil_last" ) 并在两个时间戳中报告的遥测值。 需要参考关联的传感器型号 (如步骤2中所述) 解释报告值的类型和单位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 时序见解 (TSI) 查询

FarmBeats 利用 [Azure 时序见解 (TSI) ](https://azure.microsoft.com/services/time-series-insights/) 来引入、存储、查询和可视化 IoT 规模的数据，这是高度过程并针对时序优化的数据。

在 EventHub 上接收到遥测数据，然后处理该数据并将其推送到 FarmBeats 资源组中的一个 TSI 环境。 然后，可以直接从 TSI 查询数据。 有关详细信息，请参阅 [TSI 文档](../../time-series-insights/time-series-insights-explorer.md)

按照以下步骤在 TSI 上实现数据可视化：

1. 请访问 **Azure 门户**  >  **FarmBeats DataHub 资源组** > 选择 **时序见解** 环境，>)  ( **Data Access Policies** 添加具有读取者或参与者访问权限的用户。
2. 请参阅时序 **见解** 环境 () 的 " **概述** " 页，然后选择 "时序 **见解资源管理器 URL** "。 现在，你将能够直观显示引入遥测数据。

除了存储、查询和可视化遥测以外，TSI 还支持集成到 Power BI 仪表板。 有关详细信息，请参阅 [此处]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>后续步骤

你现在已从 Azure FarmBeats 实例查询传感器数据。 现在，了解如何为你的场 [生成地图](generate-maps-in-azure-farmbeats.md#generate-maps) 。