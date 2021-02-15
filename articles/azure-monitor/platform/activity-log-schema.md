---
title: Azure 活动日志事件架构
description: 介绍 Azure 活动日志中每个类别的事件架构。
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 9bda92667cfc3afb44a55adf3f3c12798a734ddc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522713"
---
# <a name="azure-activity-log-event-schema"></a>Azure 活动日志事件架构
[Azure 活动日志](platform-logs-overview.md)可以方便用户深入了解 Azure 中发生的任何订阅级别事件。 本文介绍活动日志类别以及每个类别的架构。 

根据访问日志的方式，架构会有所不同：
 
- 本文中所述的架构基于从 [REST API](/rest/api/monitor/activitylogs) 访问活动日志。 这也是在 Azure 门户中查看事件时，选择“JSON”选项时使用的架构。
- 对于使用[诊断设置](diagnostic-settings.md)将活动日志发送到 Azure 存储或 Azure 事件中心时的架构，请参阅最后一节[来自存储帐户和事件中心的架构](#schema-from-storage-account-and-event-hubs)。
- 对于使用[诊断设置](diagnostic-settings.md)将活动日志发送到 Log Analytics 工作区时的架构，请参阅[Azure Monitor 数据参考](/azure/azure-monitor/reference/)。

## <a name="severity-level"></a>严重级别
活动日志中的每个条目都有严重性级别。 严重性级别可以具有以下值之一：  

| severity | 说明 |
|:---|:---|
| 严重 | 需要系统管理员立即关注的事件。 可能指示应用程序或系统失败或已停止响应。
| 错误 | 指示存在问题的事件，但这些事件无需立即关注。
| 警告 | 提供潜在问题而不是实际错误的预警的事件。 指示资源未处于理想状态，以后可能降级为显示错误或严重事件。  
| 信息 | 向管理员传递非关键信息的事件。 类似于这样一个备注：“仅供参考”。 

每个资源提供程序的开发人员都会选择其资源条目的严重性级别。 因此，根据应用程序的生成方式，实际的严重性可能会有所不同。 例如，对于单独获取的特定资源来说，“关键”项可能不如对 Azure 应用程序极为重要的资源类型中的“错误”那么重要。 确定要针对其发出警报的事件时，请务必考虑这一事实。  

## <a name="categories"></a>Categories
活动日志中的每个事件都有特定的类别，该类别在下表中进行了描述。 请参阅以下各部分，了解从门户、PowerShell、CLI 和 REST API 访问活动日志时，每个类别及其架构的详细信息。 [将活动日志流式传输到存储或事件中心](./resource-logs.md#send-to-azure-event-hubs)时，架构是不同的。 本文最后一个部分提供了这些属性到[资源日志架构](./resource-logs-schema.md)的映射。

| 类别 | 说明 |
|:---|:---|
| [管理](#administrative-category) | 包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 管理事件示例包括创建虚拟机和删除网络安全组。<br><br>使用资源管理器的用户或应用程序执行的每个操作都作为对特定资源类型的操作进行建模。 如果操作类型为写入、删除或操作，则会在“管理”类别中记录该操作的启动和成功或失败记录。 管理事件还包括对订阅中 Azure 基于角色的访问控制的任何更改。 |
| [服务运行状况](#service-health-category) | 包含对任何发生在 Azure 中的服务运行状况事件的记录。 SQL Azure 在美国东部的一个服务运行状况事件示例是遭遇停机。 <br><br>服务运行状况事件分 6 种：需要操作、协助恢复、事件、维护、信息或安全性。 仅当订阅中有某个资源受事件影响时，才会创建这些事件。
| [资源运行状况](#resource-health-category) | 包含对 Azure 资源发生的任何资源运行状况事件的记录。 资源运行状况事件的一个示例是，虚拟机运行状况状态更改为“不可用”。<br><br>资源运行状况事件可以表现出以下四种运行状况状态之一：“Available”、“Unavailable”、“Degraded”和“Unknown”   。 此外，资源运行状况事件可以分为“平台启动”或“用户启动” 。 |
| [Alert](#alert-category) | 包含 Azure 警报的激活记录。 警报事件的一个示例是，在过去 5 分钟内，我的 VM 上的 CPU % 始终超过 80。|
| [自动缩放](#autoscale-category) | 包含基于自动缩放设置（在订阅中定义）的自动缩放引擎操作相关的事件记录。 自动缩放事件的一个示例是，自动缩放纵向扩展操作失败。 |
| [建议](#recommendation-category) | 包含 Azure 顾问提供的建议事件。 |
| [安全性](#security-category) | 包含 Azure 安全中心生成的任何警报记录。 安全事件的一个示例是，执行了可疑的双扩展文件。 |
| [策略](#policy-category) | 包含 Azure Policy 执行的所有效果操作的记录。 Policy 事件的示例包括审核和拒绝。 Policy 执行的每个操作建模为对资源执行的操作。 |

## <a name="administrative-category"></a>管理类别
此类别包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 此类别中的事件类型的示例包括“创建虚拟机”和“删除网络安全组”。用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为“写入”、“删除”或“操作”，则该操作的开始、成功或失败记录都会记录在管理类别中。 管理类别还包括对订阅中 Azure 基于角色的访问控制的任何更改。

### <a name="sample-event"></a>示例事件
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| authorization |事件的 Azure RBAC 属性的 Blob。 通常包括“action”、“role”和“scope”属性。 |
| caller |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的电子邮件地址。 |
| channels |以下值之一：“Admin”、“Operation” |
| 声明 |Active Directory 使用 JWT 令牌来验证用户或应用程序，以在资源管理器中执行此操作。 |
| correlationId |通常为字符串格式的 GUID。 共享 correlationId 的事件属于同一 uber 操作。 |
| description |事件的静态文本说明。 |
| eventDataId |事件的唯一标识符。 |
| eventName | 管理事件的易记名称。 |
| category | 始终为“Administrative” |
| httpRequest |描述 Http 请求的 Blob。 通常包括 "clientRequestId"、"clientIpAddress" 和 "method"（HTTP 方法， 例如 PUT）。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |受影响资源的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称 |
| resourceType | 受“管理”事件影响的资源类型。 |
| ResourceId |受影响资源的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| 状态 |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus |通常为相应 REST 调用的 HTTP 状态代码，但也可能包括用于描述子状态的其他字符串，例如以下常用值：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）、Gateway Timeout（HTTP 状态代码：504）。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="service-health-category"></a>服务运行状况类别
此类别包含对任何发生在 Azure 中的服务运行状况事件的记录。 此类别的一个事件类型示例是“美国东部的 SQL Azure 正处于故障时间”。 服务运行状况事件分 5 种：必需操作、辅助恢复、事件、维护、信息或安全性，仅当订阅中存在受事件影响的资源时，它们才出现。

### <a name="sample-event"></a>示例事件
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
请参阅[服务运行状况通知](../../service-health/service-notifications.md)一文，获取有关属性的值的说明。

## <a name="resource-health-category"></a>资源运行状况类别
此类别包含 Azure 资源发生的任何资源运行状况事件的记录。 你将在此类别中看到的事件类型的示例是“虚拟机运行状况已更改为不可用”。 资源运行状况事件可以表示四种运行状况之一：Available、Unavailable、Degraded 和 Unknown。 此外，资源运行状况事件可以分为“平台启动”或“用户启动”。

### <a name="sample-event"></a>示例事件

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| channels | 始终是“Admin, Operation” |
| correlationId | 字符串格式的 GUID。 |
| description |警报事件的静态文本说明。 |
| eventDataId |警报事件的唯一标识符。 |
| category | 始终为“ResourceHealth” |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| level |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose” |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| resourceGroupName |包含资源的资源组的名称。 |
| resourceProviderName |始终为“Microsoft.Resourcehealth/healthevent/action”。 |
| resourceType | 受“资源运行状况”事件影响的资源类型。 |
| ResourceId | 受影响资源的资源 ID 的名称。 |
| 状态 |描述运行状况事件状态的字符串。 值可以是：Active、Resolved、InProgress、Updated。 |
| subStatus | 对警报而言通常为 NULL。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。|
| properties.title | 用于描述资源运行状况的用户友好字符串。 |
| properties.details | 用于描述有关事件的更多详细信息的用户友好字符串。 |
| properties.currentHealthStatus | 资源的当前运行状况。 以下值之一：“Available”、“Unavailable”、“Degraded”和“Unknown”。 |
| properties.previousHealthStatus | 资源的前一运行状况。 以下值之一：“Available”、“Unavailable”、“Degraded”和“Unknown”。 |
| properties.type | 资源运行状况事件的类型说明。 |
| properties.cause | 资源运行状况事件的原因说明。 “UserInitiated”和“PlatformInitiated”。 |


## <a name="alert-category"></a>警报类别
此类别包含所有经典 Azure 警报的激活记录。 可在此类别中看到的事件类型示例如“过去 5 分钟内，myVM 上的 CPU 百分比已超过 80%”。 许多 Azure 系统都具有警报概念 - 可定义某种类型的规则，并在条件匹配该规则时接收通知。 每当支持的 Azure 警报类型“激活”或满足生成通知的条件时，激活记录也会推送到此类别的活动日志中。

### <a name="sample-event"></a>示例事件

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| caller | 始终是 Microsoft.Insights/alertRules |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或警报引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |警报事件的静态文本说明。 |
| eventDataId |警报事件的唯一标识符。 |
| category | 始终为“Alert” |
| level |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |受影响资源的资源组的名称（如果是指标警报）。 对于其他警报类型，它是包含警报本身的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称（如果是指标警报）。 对于其他警报类型，它是警报本身的资源提供程序的名称。 |
| ResourceId | 受影响资源的资源 ID 的名称（如果是指标警报）。 对于其他警报类型，它是警报资源本身的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| 状态 |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对警报而言通常为 NULL。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

### <a name="properties-field-per-alert-type"></a>每种警报类型的属性字段
该属性字段包含不同的值，具体取决于警报事件的源。 两种常见警报事件提供程序是活动日志警报和指标警报。

#### <a name="properties-for-activity-log-alerts"></a>活动日志警报的属性
| 元素名称 | 说明 |
| --- | --- |
| properties.subscriptionId | 激活活动日志预警规则的活动日志事件的订阅 ID。 |
| properties.eventDataId | 激活活动日志预警规则的活动日志事件的事件数据 ID。 |
| properties.resourceGroup | 激活活动日志预警规则的活动日志事件的资源组。 |
| properties.resourceId | 激活活动日志预警规则的活动日志事件的资源 ID。 |
| properties.eventTimestamp | 激活活动日志预警规则的活动日志事件的事件时间戳。 |
| properties.operationName | 激活活动日志预警规则的活动日志事件的操作名称。 |
| properties.status | 激活活动日志预警规则的活动日志事件的状态。|

#### <a name="properties-for-metric-alerts"></a>指标警报的属性
| 元素名称 | 说明 |
| --- | --- |
| properties.RuleUri | 指标预警规则自身的资源 ID。 |
| properties.RuleName | 指标预警规则的名称。 |
| properties.RuleDescription | 对指标预警规则的说明（如预警规则中的定义）。 |
| properties.Threshold | 计算指标预警规则所用的阈值。 |
| properties.WindowSizeInMinutes | 计算指标预警规则所用的窗口大小。 |
| properties.Aggregation | 在指标预警规则中定义的聚合类型。 |
| properties.Operator | 计算指标预警规则所用的条件运算符。 |
| properties.MetricName | 计算指标预警规则所用的指标的指标名称。 |
| properties.MetricUnit | 计算指标预警规则所用的指标的指标单位。 |

## <a name="autoscale-category"></a>自动缩放类别
此类别包含基于在订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的事件记录。 可在此类别中看到的事件类型示例如“自动缩放扩展操作失败”。 使用自动缩放，可在支持的资源类型中，通过自动缩放设置基于日期和/或负载（指标）数据来自动增加或减少实例的数量。 满足纵向扩展或缩减条件时，开始、成功或失败的事件将记录到此类别中。

### <a name="sample-event"></a>示例事件
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| caller | 始终是 Microsoft.Insights/autoscaleSettings |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或自动缩放引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |自动缩放事件的静态文本说明。 |
| eventDataId |自动缩放事件的唯一标识符。 |
| level |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |自动缩放设置的资源组名称。 |
| resourceProviderName |自动缩放设置的资源提供程序名称。 |
| ResourceId |自动缩放设置的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| properties.Description | 有关自动缩放引擎执行的操作的详细说明。 |
| properties.ResourceName | 受影响资源（正在执行缩放操作的资源）的资源 ID |
| properties.OldInstancesCount | 自动缩放操作生效前的实例数量。 |
| properties.NewInstancesCount | 自动缩放操作生效后的实例数量。 |
| properties.LastScaleActionTime | 自动缩放操作发生时的时间戳。 |
| 状态 |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对自动缩放而言通常为 NULL。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="security-category"></a>安全类别
此类别包含 Azure 安全中心生成的任何警报记录。 可在此类别中看到的事件类型示例为“执行了可疑的双扩展名文件”。

### <a name="sample-event"></a>示例事件
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| channels | 始终为“运行” |
| correlationId | 字符串格式的 GUID。 |
| description |安全事件的静态文本说明。 |
| eventDataId |安全事件的唯一标识符。 |
| eventName |安全事件的友好名称。 |
| category | 始终为“Security” |
| ID |安全事件的唯一资源标识符。 |
| level |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”或“Informational” |
| resourceGroupName |资源的资源组名称。 |
| resourceProviderName |Azure 安全中心的资源提供程序名称。 始终为“Microsoft.Security”。 |
| resourceType |生成安全事件的资源的类型，如“Microsoft.Security/locations/alerts” |
| ResourceId |安全警报的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 这些属性将因安全警报的类型而异。 有关来自安全中心的警报类型的说明，请参阅[此页](../../security-center/security-center-alerts-overview.md)。 |
| properties.Severity |严重性级别。 可能的值为“High”、“Medium”或“Low”。 |
| 状态 |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对于安全事件通常为 null。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="recommendation-category"></a>建议类别
此类别包含为服务生成的任何新建议的记录。 建议的示例将为“使用可用性集提高容错能力”。 可以生成以下四种类型的“建议”事件：高可用性、性能、安全性和成本优化。 

### <a name="sample-event"></a>示例事件
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| channels | 始终为“运行” |
| correlationId | 字符串格式的 GUID。 |
| description |建议事件的静态文本说明 |
| eventDataId | 建议事件的唯一标识符。 |
| category | 始终为“Recommendation” |
| ID |建议事件的唯一资源标识符。 |
| level |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”或“Informational” |
| operationName |操作的名称。  始终为“Microsoft.Advisor/generateRecommendations/action”|
| resourceGroupName |资源的资源组名称。 |
| resourceProviderName |此建议适用的资源的资源提供程序名称，例如“MICROSOFT.COMPUTE” |
| resourceType |此建议适用的资源的资源类型名称，例如“MICROSOFT.COMPUTE/virtualmachines” |
| ResourceId |此建议适用的资源的资源 ID |
| 状态 | 始终为“Active” |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |
| properties |`<Key, Value>` 对集（即字典），描述建议的详细信息。|
| properties.recommendationSchemaVersion| 在活动日志条目中发布的建议属性的架构版本 |
| properties.recommendationCategory | 建议的类别。 可能的值为“High Availability”、“Performance”、“Security”和“Cost” |
| properties.recommendationImpact| 建议的影响。 可能的值为“High”、“Medium”、“Low” |
| properties.recommendationRisk| 建议的风险。 可能的值为“Error”、“Warning”、“None” |

## <a name="policy-category"></a>策略类别

此类别包含 [Azure Policy](../../governance/policy/overview.md) 执行的所有效果操作的记录。 在此类别中看到的事件类型示例包括“审核”和“拒绝”。  Policy 执行的每个操作建模为对资源执行的操作。

### <a name="sample-policy-event"></a>示例 Policy 事件

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Policy 事件属性说明

| 元素名称 | 说明 |
| --- | --- |
| authorization | 事件的 Azure RBAC 属性的数组。 对于新资源，这是触发评估的请求的操作和范围。 对于现有资源，操作是“Microsoft.Resources/checkPolicyCompliance/read”。 |
| caller | 对于新资源，为启动部署的标识。 对于现有资源，为 Microsoft Azure Policy Insights RP 的 GUID。 |
| channels | Policy 事件仅使用“操作”通道。 |
| 声明 | Active Directory 使用 JWT 令牌来验证用户或应用程序，以在资源管理器中执行此操作。 |
| correlationId | 通常为字符串格式的 GUID。 共享 correlationId 的事件属于同一 uber 操作。 |
| description | 对于 Policy 事件，此字段是空白的。 |
| eventDataId | 事件的唯一标识符。 |
| eventName | “BeginRequest”或“EndRequest”。 “BeginRequest”用于延迟的 auditIfNotExists 和 deployIfNotExists 评估，并且在 deployIfNotExists 效果启动模板部署时使用。 所有其他操作返回“EndRequest”。 |
| category | 将活动日志事件声明为属于“Policy”。 |
| eventTimestamp | 处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| ID | 特定资源中的事件的唯一标识符。 |
| level | 事件的级别。 审核使用“警告”，拒绝使用“错误”。 auditIfNotExists 或 deployIfNotExists 错误可以根据严重性生成“警告”或“错误”。 所有其他 Policy 事件使用“信息”。 |
| operationId | 在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName | 操作的名称，与策略效果直接相关。 |
| resourceGroupName | 评估的资源的资源组名称。 |
| resourceProviderName | 评估的资源的资源提供程序名称。 |
| resourceType | 对于新资源，它是评估的类型。 对于现有资源，返回“Microsoft.Resources/checkPolicyCompliance”。 |
| ResourceId | 评估的资源的资源 ID。 |
| 状态 | 用于描述 Policy 评估结果状态的字符串。 大多数 Policy 评估返回“成功”，但拒绝效果返回“失败”。 auditIfNotExists 或 deployIfNotExists 中的错误也返回“失败”。 |
| subStatus | 对于 Policy 事件，此字段是空白的。 |
| submissionTimestamp | 事件可供查询的时间戳。 |
| subscriptionId | Azure 订阅 ID。 |
| properties.isComplianceCheck | 部署新资源或更新现有资源的资源管理器属性时，将返回“False”。 所有其他[评估触发器](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)会生成“True”。 |
| properties.resourceLocation | 所评估资源的 Azure 区域。 |
| properties.ancestors | 从直接父级到最远祖父级排序的父管理组的逗号分隔列表。 |
| properties.policies | 包括有关生成此策略评估结果的策略定义、分配、影响和参数的详细信息。 |
| relatedEvents | 对于 Policy 事件，此字段是空白的。 |


## <a name="schema-from-storage-account-and-event-hubs"></a>来自存储帐户和事件中心的架构
将 Azure 活动日志流式传输到存储帐户或事件中心时，数据遵循[资源日志架构](./resource-logs-schema.md)。 下表提供从上述架构到资源日志架构的属性映射。

> [!IMPORTANT]
> 写入到存储帐户的活动日志数据的格式已在 2018 年 11 月 1 日更改为 JSON Lines。 若要详细了解此格式更改，请参阅[为存档到存储帐户的 Azure Monitor 资源日志的格式更改做准备](./resource-logs-blob-format.md)。


| 资源日志架构属性 | 活动日志 REST API 架构属性 | 注释 |
| --- | --- | --- |
| time | eventTimestamp |  |
| ResourceId | ResourceId | subscriptionId、resourceType 和 resourceGroupName 都是从 resourceId 推断而来。 |
| operationName | operationName.value |  |
| category | 操作名称的一部分 | 操作类型分类：“写入”/“删除”/“操作” |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | 空值 | 始终为 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | 声明和授权属性 |  |
| Level | Level |  |
| location | 空值 | 处理事件的位置。 这不是资源所在位置，而是处理事件的位置。未来更新中将删除此属性。 |
| 属性 | properties.eventProperties |  |
| properties.eventCategory | category | 如果不存在 properties.eventCategory，则 category 是“管理” |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |

下面是使用此架构的事件的示例。

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>后续步骤
* [了解有关活动日志的更多信息](platform-logs-overview.md)
* [创建诊断设置，以便将活动日志发送到 Log Analytics 工作区、Azure 存储或事件中心](diagnostic-settings.md)
