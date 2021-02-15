---
title: Azure 通信服务的区域可用性和数据驻留
description: 了解 Azure 通信服务的数据驻留和隐私相关事项
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7c522abd04f4a3e480bb5c3e14e78cc03dbd5d86
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888634"
---
# <a name="region-availability-and-data-residency"></a>区域可用性和数据驻留

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通信服务致力于帮助我们的客户满足他们的隐私和个人数据需求。 若使用通信服务的开发人员与使用该应用程序的人有直接关系，便可成为其数据的控制者。 由于 Azure 通信服务代表你存储了这些数据，因此我们很可能成为这些数据的处理者。 本页概述该服务如何保留数据以及你如何识别、导出和删除此数据。

## <a name="data-residency"></a>数据驻留

创建通信服务资源时，需要指定一个地理位置（非 Azure 数据中心）。 通信服务静态存储的所有数据都将保留在该地理位置中，数据中心由通信服务在内部选择。 但数据可在其他地理位置传输或处理，因此，必需使用这些全局终结点来向最终用户提供高性能、低延迟的体验，无论他们位于何处。

## <a name="relating-humans-to-azure-communication-services-identities"></a>将人类与 Azure 通信服务标识关联起来

应用程序管理人类用户和通信服务标识之间的关系。 若要删除人类用户的数据，必须删除涉及与该用户相关的所有通信服务标识的数据。

有两类通信服务数据：
- **API 数据。** 此类数据由通信服务 API 创建和管理，一个典型示例是通过 Chat API 管理聊天消息。
- **Azure Monitor 日志。** 此类数据由服务创建，并通过 Azure Monitor 数据平台进行管理。 此类数据包括遥测和指标，可帮助你了解通信服务的使用情况。 此类数据不由通信服务 API 管理。

## <a name="api-data"></a>API 数据

### <a name="identities"></a>标识

Azure 通信服务维护标识目录，使用 [DeleteIdentity](/rest/api/communication/communicationidentity/delete) API 来删除标识。 删除标识将撤销所有关联的访问令牌，并删除其聊天消息。 若要详细了解如何删除标识，[请参阅本页面](../quickstarts/access-tokens.md)。

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure 资源管理器

将 Azure 门户或 Azure 资源管理器 API 与通信服务结合使用，可以创建个人数据。 [通过本页面了解如何管理 Azure 资源管理器系统中的个人数据。](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>电话号码管理

Azure 通信服务维护与通信服务资源关联的电话号码目录。 使用这些 API 可检索电话号码并将其删除：
- `Release Phone Number`

### <a name="chat"></a>聊天

聊天线程和消息将被保留，直到被显式删除。 完全空闲的线程在 30 天后会自动删除。 使用 [Chat API](/rest/api/communication/chat/deletechatmessage/deletechatmessage) 获取、列出、更新和删除消息。

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

发出和收到的短信由服务临时处理，不会保留。 

### <a name="pstn-voice-calling"></a>PSTN 语音呼叫

音频和视频通信由服务临时处理，不会在 Azure Monitor 日志以外的其他资源中保留任何数据。

### <a name="internet-voice-and-video-calling"></a>Internet 语音和视频呼叫

音频和视频通信由服务临时处理，不会在 Azure Monitor 日志以外的其他资源中保留任何数据。

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor 和 Log Analytics

Azure 通信服务将向 Azure Monitor 提供日志记录数据，目的是了解该服务的运行状况和使用情况。 其中一些日志包含字段数据形式的通信服务标识和电话号码。 若要删除任何潜在的个人数据，[请对 Azure Monitor 使用这些过程](../../azure-monitor/platform/personal-data-mgmt.md)。 可能还需配置 [Azure Monitor 的默认保留期](../../azure-monitor/platform/manage-cost-storage.md)。

## <a name="additional-resources"></a>其他资源

- [针对 GDPR 和 CCPA 的 Azure 数据主体请求](/microsoft-365/compliance/gdpr-dsr-azure?preserve-view=true&view=o365-worldwide)
- [Microsoft 信任中心](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure 交互式地图 - 客户数据位于何处？](https://azuredatacentermap.azurewebsites.net/)