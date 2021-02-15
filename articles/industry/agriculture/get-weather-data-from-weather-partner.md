---
title: 从天气合作伙伴获取天气数据
description: 本文介绍如何从合作伙伴获取天气数据。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460876"
---
# <a name="get-weather-data-from-weather-partners"></a>从天气合作伙伴获取天气数据

Azure FarmBeats 可帮助你使用基于 Docker 的连接器框架从天气数据提供商处引入天气数据。 使用此框架，天气数据提供程序实现了可与 FarmBeats 集成的 Docker。 目前支持以下天气数据提供程序。

  ![FarmBeats 合作伙伴](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

天气数据可用于在 FarmBeats 中生成可操作的见解和构建 AI 模型。

## <a name="before-you-start"></a>开始之前

若要获取天气数据，请确保已 [安装 FarmBeats](./install-azure-farmbeats.md)。 1.2.11 和更高版本中支持天气集成。 

## <a name="enable-weather-integration-with-farmbeats"></a>启用与 FarmBeats 的天气集成

开始在 FarmBeats Datahub 上获取天气数据的步骤：

1. 中转到你的 FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` 。

2. 中转到/Partner API，然后发出 POST 请求。 使用以下输入有效负载：

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   例如，若要从 DTN 获取天气数据，请使用以下有效负载。 您可以根据自己的喜好修改名称和描述。

   > [!NOTE]
   > 以下步骤需要 API 密钥。 若要获取 DTN 订阅的密钥，请联系 DTN。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > 有关 partner 对象的详细信息，请参阅本文中的 [附录](get-weather-data-from-weather-partner.md#appendix) 。

   上一步将设置资源，以使 Docker 在客户的 FarmBeats 环境中运行。  

   设置资源大约需要10到15分钟。

3. 检查上一步中创建的/Partner 对象的状态。 若要检查状态，请在/Partner API 上发出 GET 请求，并检查伙伴对象的状态。 FarmBeats 成功预配合作伙伴后，状态将设置为 " **活动**"。

4. 在/JobType API 上发出 GET 请求。 检查你之前在合作伙伴添加过程中创建的天气作业。 在天气作业中， **pipelineName** 字段采用以下格式 **name_partner： type_job-name**。

      现在，FarmBeats 实例具有活动的天气数据伙伴。 可以运行作业来请求特定位置 (纬度和经度) 和日期范围内的天气数据。 作业类型将包含运行天气作业所需的参数的详细信息。

      例如，对于 DTN，将创建以下作业类型：
   
      - **get_dtn_daily_observations**：获取某个位置和时间段的每日观察。
      - **get_dtn_daily_forecasts**：获取某个位置和时间段的每日预测。
      - **get_dtn_hourly_observations**：获取针对某个位置和时间段的每小时观察。
      - **get_dtn_hourly_forecasts**：获取某个位置和时间段的每小时预测。

6. 记下 "ID" 和 "作业类型" 的参数。

7. 请参阅/Jobs API 并在/Jobs. 上发出 POST 请求 使用以下输入有效负载：

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   例如，若要运行 **get_dtn_daily_observations**，请使用以下有效负载：

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 上一步按照合作伙伴 Docker 中的定义运行天气作业，并将天气数据引入到 FarmBeats 中。 可以通过在/Jobs. 上发出 GET 请求来检查作业的状态 在响应中，查找 **currentState**。 完成后， **currentState** 设置为 **Succeeded**。

## <a name="query-ingested-weather-data"></a>查询引入天气数据

天气作业完成后，可以使用 FarmBeats Datahub REST Api 查询引入天气数据，以生成模型或可操作的见解。

使用 FarmBeats REST API 查询天气数据的步骤：

1. 在 FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)中转到/WeatherDataLocation API 并发出 GET 请求。 响应包括为作业运行指定 (纬度和经度) 创建的/WeatherDataLocation 对象。 记下 " **ID** " 和 " **weatherDataModelId** " 对象。

2. 像以前一样，在 **weatherDataModelId** 的/WeatherDataModel API 上发出 GET/{id} 请求。 天气数据模型显示所有元数据，以及有关引入天气数据的详细信息。 例如，在天气数据模型对象中，天气度量详细说明了支持哪些天气信息以及哪些类型和单位。 例如：

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   记下天气预报的 GET/{id} 调用的响应。

3. 中转到遥测 API 并发出 POST 请求。 使用以下输入有效负载：

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    响应显示指定时间范围内可用的天气数据：

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

在前面的示例中，响应显示两个时间戳的数据。 它还会显示两个时间戳中报告的天气数据的度量值名称 (温度) 和值。 请参阅关联的天气预报数据模型，解释报告值的类型和单位。

## <a name="troubleshoot-job-failures"></a>排查作业故障

若要对作业失败进行故障排除，请 [检查作业日志](troubleshoot-azure-farmbeats.md#weather-data-job-failures)。


## <a name="appendix"></a>附录

|        Partner   |  详细信息   |
| ------- | -------             |
|     DockerDetails-imageName         |          Docker 映像名称。 例如，docker.io/mydockerimage (映像 hub.docAzure 容器注册表中的 ker.com) 或 myazureacr.azurecr.io/mydockerimage (图像) 等。 如果未提供任何注册表，则默认值为 ker.com hub.doc。      |
|          DockerDetails-imageTag             |         Docker 映像的标记名称。 默认值为 "最新"。     |
|  DockerDetails-凭据      |  用于访问专用 Docker 的凭据。 合作伙伴提供凭据。   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU。 有关详细信息，请参阅 [所有可用的 Linux 虚拟机](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 <BR> <BR> 有效值为 "Small"、"超大型"、"Small"、"A8"、"A9"、"Medium"、"A5"、"A6"、"A7"、"STANDARD_D1"、"STANDARD_D2"、"STANDARD_D3"、"STANDARD_D4"、"STANDARD_D11"、"STANDARD_D12"、"STANDARD_D13"、"STANDARD_D14"、"A10"、"A11"、"STANDARD_D1_V2"、"STANDARD_D2_V2"、"STANDARD_D3_V2"、"STANDARD_D4_V2"、"STANDARD_D11_V2"、"STANDARD_D12_V2"、"STANDARD_D13_V2"、"STANDARD_D14_V2"、"STANDARD_G1"、"STANDARD_G2"、"STANDARD_G3"、"STANDARD_G4"，"STANDARD_G5"、"STANDARD_D5_V2"、"BASIC_A1"、"BASIC_A2"、"BASIC_A3"、"BASIC_A4"、"STANDARD_A1"、"STANDARD_A2"、"STANDARD_A3"、"STANDARD_A4"、"STANDARD_A5"、"STANDARD_A6"、"STANDARD_A7"、"STANDARD_A8"、"STANDARD_A9"、"STANDARD_A10"、"STANDARD_A11"、"STANDARD_D15_V2"、"STANDARD_F1"、"STANDARD_F2"、"STANDARD_F4"、"STANDARD_F8"、"STANDARD_F16"、"STANDARD_NV6"、"STANDARD_NV12"、"STANDARD_NV24"、"STANDARD_NC6"、"STANDARD_NC12"、"STANDARD_NC24"、"STANDARD_NC24r"，"STANDARD_H8"、"STANDARD_H8m"、"STANDARD_H16"、"STANDARD_H16m"、"STANDARD_H16mr"、"STANDARD_H16r"、"STANDARD_A1_V2"、"STANDARD_A2_V2"、"STANDARD_A4_V2"、"STANDARD_A8_V2"、"STANDARD_A2m_V2"、"STANDARD_A4m_V2"、"STANDARD_A8m_V2"、"STANDARD_M64ms"、"STANDARD_M128s" 和 "STANDARD_D2_V3"。 *默认值为 "STANDARD_D2_V2"。*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  每个批处理池的专用计算机节点数。 默认值为 1。 |
|    DockerDetails-azureBatchVMDetails-nodeAgentSKUID          |    Azure Batch 节点代理 SKU ID。 目前仅支持 "batch. ubuntu 18.04" 批处理节点代理。    |
| DockerDetails - partnerCredentials | 用于在 Docker 中调用合作伙伴 API 的凭据。 合作伙伴基于支持的授权机制提供此信息;例如，用户名和密码，或 API 密钥。 |
| partnerType | "天气"。 FarmBeats 中的其他合作伙伴类型为 "传感器" 和 "图像"。  |
|  name   |   FarmBeats 系统中的合作伙伴的所需名称。   |
|  description |  说明。   |

## <a name="next-steps"></a>后续步骤

现在，你已从 Azure FarmBeats 实例查询了传感器数据，接下来请了解如何为你的场 [生成地图](generate-maps-in-azure-farmbeats.md#generate-maps) 。