---
title: 查看 Azure Data Lake Storage Gen1 诊断日志 | Microsoft Docs
description: '了解如何设置和访问 Azure Data Lake Storage Gen1 的诊断日志 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 07bf22cfc683d8c6f2c765364334ed1594e2fdaa
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745878"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>访问 Azure Data Lake Storage Gen1 的诊断日志
了解如何启用 Azure Data Lake Storage Gen1 帐户诊断日志记录以及如何查看为帐户收集的日志。

组织可以为其 Azure Data Lake Storage Gen1 帐户启用诊断日志记录，以收集数据访问审核跟踪，以提供信息，如访问数据的用户列表、访问数据的频率、存储在帐户中的数据量等。启用后，会尽力记录诊断和/或请求。 仅在针对服务终结点发出请求时才会创建请求和诊断日志条目。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>对 Data Lake Storage Gen1 帐户启用诊断日志记录
1. 登录到新 [Azure 门户](https://portal.azure.com)。
2. 打开 Data Lake Storage Gen1 帐户，在 Data Lake Storage Gen1 帐户边栏选项卡中单击“诊断设置”。
3. 在“诊断设置”边栏选项卡中，单击“启用诊断”。

    ![Data Lake Storage 第1代帐户的屏幕截图，其中包含诊断设置选项和 "打开诊断" 选项。](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "启用诊断日志")

3. 在“诊断设置”边栏选项卡上，作出以下更改来配置诊断日志记录。
   
    !["诊断设置" 部分的屏幕截图，其中包含名称文本框和 "保存" 选项。](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "启用诊断日志")
   
   * 对于“名称”，请输入诊断日志配置的值。
   * 可选择以不同的方式存储/处理数据。
     
        * 选择选项“存档到存储帐户”将日志存储到 Azure 存储帐户。 要存档以后会进行批处理的数据时使用此选项。 如果选择此选项，必须提供一个要将日志保存到的 Azure 存储帐户。
        
        * 选择选项“流式传输到事件中心”将日志数据流式传输到 Azure 事件中心。 具有下游处理管道来实时分析传入日志时最可能使用此选项。 若选择此选项，必须提供要使用的 Azure 事件中心的详细信息。

        * 选择要 **发送到 Log Analytics** 的选项，以使用 Azure Monitor 服务分析生成的日志数据。 如果选择此选项，必须提供要用于执行日志分析的 Log Analytics 工作区的详细信息。 有关使用 Azure Monitor 日志的详细信息，请参阅 [查看或分析使用 Azure Monitor 日志收集的数据](../azure-monitor/log-query/log-analytics-tutorial.md) 。
     
   * 指定是要获取审核日志还是请求日志，或者两者。
   * 指定数据必须保留的天数。 保留期仅在使用 Azure 存储帐户存档日志数据时才适用。
   * 单击“保存” 。

启用诊断设置后，可在“诊断日志”选项卡中查看日志。

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>查看 Data Lake Storage Gen1 帐户诊断日志
查看 Data Lake Storage Gen1 帐户日志数据有两种方式。

* 从 Data Lake Storage Gen1 帐户设置视图查看
* 从存储数据的 Azure 存储帐户查看

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>使用 Data Lake Storage Gen1 设置视图
1. 从 Data Lake Storage Gen1 帐户的“设置”边栏选项卡，单击“诊断日志”。
   
    ![查看诊断日志](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "查看诊断日志") 
2. 在“诊断日志”边栏选项卡上，可看到按“审核日志”和“请求日志”分类的日志。
   
   * 请求日志捕获 Data Lake Storage Gen1 帐户上作出的每个 API 请求。
   * 审核日志类似于请求日志，但提供 Data Lake Storage Gen1 帐户上正在执行的操作的更详细的分解结构。 例如，一个单一上传 API 调用请求日志可能导致审核日志中有多个“附加”操作。
3. 要下载日志，请单击每个日志条目的“下载”链接。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>从包含日志数据的 Azure 存储帐户查看
1. 打开与日志记录的 Data Lake Storage Gen1 关联的 Azure 存储帐户边栏选项卡，并单击“Blob”。 “Blob 服务”边栏选项卡上会列出两个容器。
   
    ![Data Lake Storage 第1代边栏选项卡的屏幕截图选择 "Blob" 选项，并在 "博客服务" 边栏选项卡中提供两个称为 "blob 服务" 的名称。](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "查看诊断日志")
   
   * 容器 **insights-logs-audit** 包含审核日志。
   * 容器 **insights-logs-requests** 包含请求日志。
2. 在这些容器中，日志存储在以下结构下。
   
    ![在容器中存储的日志结构的屏幕截图。](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "查看诊断日志")
   
    例如，审核日志的完整路径可能是 `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    类似地，请求日志的完整路径可能是 `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>了解日志数据的结构
审核和请求日志格式为 JSON。 本部分介绍请求和审核日志的 JSON 结构。

### <a name="request-logs"></a>请求日志
此处是 JSON 格式的请求日志中的一个示例条目。 每个 blob 都有一个名为 " **记录** " 的根对象，其中包含一组日志对象。

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>请求日志架构
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| time |字符串 |日志时间戳（采用 UTC） |
| ResourceId |字符串 |操作发生所在的资源的 ID |
| category |字符串 |日志类别。 例如，“请求”。 |
| operationName |字符串 |被记录的操作的名称。 例如 getfilestatus。 |
| resultType |字符串 |操作状态，例如，200。 |
| callerIpAddress |字符串 |作出请求的客户端 的IP 地址 |
| correlationId |字符串 |可用于将一组相关日志条目组合在一起的日志的 ID |
| identity |对象 |生成日志的标识 |
| properties |JSON |详细信息参见以下内容 |

#### <a name="request-log-properties-schema"></a>请求日志属性架构
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| HttpMethod |字符串 |用于此操作的 HTTP 方法。 例如 GET。 |
| `Path` |String |操作执行所在的路径 |
| RequestContentLength |int |HTTP 请求的内容长度 |
| ClientRequestId |字符串 |唯一标识此请求的 ID |
| StartTime |字符串 |服务器接收请求的时间 |
| EndTime |字符串 |服务器发送响应的时间 |
| StoreIngressSize |Long |要 Data Lake Store 的大小（以字节为单位）引入 |
| StoreEgressSize |Long |Data Lake Store 的大小（以字节为单位）出口 |
| QueryParameters |字符串 |说明： http 查询参数。 示例1： api 版本 = 2014-01-01&op = getfilestatus 示例2： op = APPEND&append = true&syncFlag = DATA&filesessionid = bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid = bee3355a-4925-4435-bb4d ceea52811aeb&偏移 = 28313319&api 版本 = 2017-08-01 |

### <a name="audit-logs"></a>审核日志
此处是 JSON 格式的审核日志中的一个示例条目。 每个 Blob 具有一个名为 **records** 的根对象，该对象包含一组日志对象

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>审核日志架构
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| time |字符串 |日志时间戳（采用 UTC） |
| ResourceId |字符串 |操作发生所在的资源的 ID |
| category |字符串 |日志类别。 例如，“审核”。 |
| operationName |字符串 |被记录的操作的名称。 例如 getfilestatus。 |
| resultType |字符串 |操作状态，例如，200。 |
| resultSignature |字符串 |有关操作的其他详细信息。 |
| correlationId |字符串 |可用于将一组相关日志条目组合在一起的日志的 ID |
| identity |对象 |生成日志的标识 |
| properties |JSON |详细信息参见以下内容 |

#### <a name="audit-log-properties-schema"></a>审核日志属性架构
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| StreamName |字符串 |操作执行所在的路径 |

## <a name="samples-to-process-the-log-data"></a>日志数据处理示例
在将日志从 Azure Data Lake Storage Gen1 发送到 Azure Monitor 日志时 (参阅使用 [Azure Monitor 日志来查看或分析收集的数据](../azure-monitor/log-query/log-analytics-tutorial.md) Azure Monitor，下面的查询将返回一个表，其中包含一个用户显示名称的列表、事件的时间以及事件时间与可视化图的事件计数。 可轻松修改该查询，以显示用户 GUID 或其他属性：

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 提供如何处理和分析日志数据的示例。 可以在中找到该示例 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) 。 

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
