---
title: 刷新 Azure Analysis Services 模型的逻辑应用 |Microsoft Docs
description: 本文介绍如何使用 Azure 逻辑应用对 Azure Analysis Services 的异步刷新进行编码。
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019080"
---
# <a name="refresh-with-logic-apps"></a>使用逻辑应用进行刷新

使用逻辑应用和 REST 调用，可以针对 Azure Analysis 表格模型执行自动数据刷新操作，包括同步查询横向扩展的只读副本。

若要详细了解如何将 REST API 与 Azure Analysis Services 配合使用，请参阅[使用 REST API 执行异步刷新](analysis-services-async-refresh.md)。

## <a name="authentication"></a>Authentication

所有调用必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份验证。  本文中的示例将使用服务主体 (SPN) 对 Azure Analysis Services 进行身份验证。 有关详细信息，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>设计逻辑应用

> [!IMPORTANT]
> 以下示例假设已禁用 Azure Analysis Services 防火墙。 如果启用了防火墙，则必须将请求发起方的公共 IP 地址添加到 Azure Analysis Services 防火墙中的已批准列表。 若要详细了解每个区域的 Azure 逻辑应用 IP 范围，请参阅 [Azure 逻辑应用的限制和配置信息](../logic-apps/logic-apps-limits-and-config.md#configuration)。

### <a name="prerequisites"></a>必备条件

#### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
创建的服务主体必须对服务器拥有服务器管理员权限。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>配置逻辑应用

在此示例中，逻辑应用设计为在收到 HTTP 请求时触发。 这样，就可以使用业务流程工具（例如 Azure 数据工厂）来触发 Azure Analysis Services 模型刷新。

创建逻辑应用后：

1. 在逻辑应用设计器中，选择“收到 HTTP 请求时”作为第一个操作。 

   ![添加已收到 HTTP 请求时的活动](./media/analysis-services-async-refresh-logic-app/1.png)

保存逻辑应用后，此步骤将会填充 HTTP POST URL。

2. 添加新步骤并搜索 **HTTP**。  

   ![选择了 "HTTP" 磁贴的 "选择操作" 部分的屏幕截图。](./media/analysis-services-async-refresh-logic-app/9.png)

   ![选择了 "HTTP-HTTP" 磁贴的 "HTTP" 窗口的屏幕截图。](./media/analysis-services-async-refresh-logic-app/10.png)

3. 选择“HTTP”以添加此操作。 

   ![添加 HTTP 活动](./media/analysis-services-async-refresh-logic-app/2.png)

按如下所示配置 HTTP 活动：

|properties  |值  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | https://服务器区域  /servers/aas 服务器名称  /models/数据库名称  /refreshes <br /> <br /> 例如： https： \/ /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**标头**     |   Content-Type、application/json <br /> <br />  ![标头](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**正文**     |   若要详细了解如何构建请求正文，请参阅[使用 REST API - POST /refreshes 执行异步刷新](analysis-services-async-refresh.md#post-refreshes)。 |
|**身份验证**     |Active Directory OAuth         |
|**租户**     |填写你的 Azure Active Directory 租户 ID         |
|**受众**     |https：//*. asazure         |
|**客户端 ID**     |输入你的服务主体名称客户端 ID         |
|**凭据类型**     |机密         |
|**机密**     |输入你的服务主体名称机密         |

示例：

![已完成 HTTP 活动](./media/analysis-services-async-refresh-logic-app/7.png)

现在请测试该逻辑应用。  在逻辑应用设计器中单击“运行”。 

![测试逻辑应用](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>通过 Azure 数据工厂使用逻辑应用

保存逻辑应用后，请查看**收到 HTTP 请求时**活动，然后复制现在生成的 **HTTP POST URL**。  Azure 数据工厂可以使用此 URL 发出异步调用来触发逻辑应用。

下面是执行此操作的示例 Azure 数据工厂 Web 活动。

![数据工厂 Web 活动](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用独立的逻辑应用

如果你不打算使用数据工厂等业务流程工具来触发模型刷新，可将逻辑应用设置为按计划触发刷新。

沿用上面的示例，请删除第一个活动，并将其替换为“计划”活动。 

![显示 "逻辑应用" 页面并选择 "计划" 磁贴的屏幕截图。](./media/analysis-services-async-refresh-logic-app/12.png)

![显示 "触发器" 页的屏幕截图。](./media/analysis-services-async-refresh-logic-app/13.png)

本示例将使用“重复周期”。 

添加活动后，配置“间隔”和“频率”，然后添加新参数并选择“在这些时间”。 

![显示 "定期" 部分的屏幕截图，其中选择了 "当时时间" 参数。](./media/analysis-services-async-refresh-logic-app/16.png)

选择所需的小时数。

![计划活动](./media/analysis-services-async-refresh-logic-app/15.png)

保存逻辑应用。

## <a name="next-steps"></a>后续步骤

[示例](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)