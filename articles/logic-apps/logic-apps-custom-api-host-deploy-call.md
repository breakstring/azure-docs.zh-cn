---
title: 调用自定义 Web API 和 REST API
description: 从 Azure 逻辑应用中调用自己的 Web API 和 REST API
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83659770"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>从 Azure 逻辑应用中的工作流部署和调用自定义 API

[创建自己的 API](./logic-apps-create-api-app.md) 以用于逻辑应用工作流后，需要先部署这些 API，然后才能对其进行调用。 虽然可以将 API 部署为 [Web 应用](../app-service/overview.md)，但请考虑将 API 部署为 [API 应用](../app-service/app-service-web-tutorial-rest-api.md)，便于更加轻松地在云端和本地生成、托管和使用 API。 不必更改 API 中的任何代码 - 可直接将代码部署到 API 应用。 可在 [Azure App Service](../app-service/overview.md) 上托管API，它是一款平台即服务 (PaaS) 产品，可提供简单的高缩放性 API 托管。

虽然可从逻辑应用调用任何 API，但为获得最佳体验，请添加 [Swagger 元数据](https://swagger.io/specification/)，用于说明 API 的操作和参数。 此 Swagger 文档有助于使 API 与逻辑应用集成更轻松、运行更顺畅。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>将 API 部署为 Web 应用或 API 应用

若要从逻辑应用调用自定义 API，首先需将 API 作为 Web 应用或 API 应用部署到 Azure 应用服务。 若要使逻辑应用设计器能够读取 Swagger 文档，请为 Web 应用或 API 应用设置 API 定义属性并开启[跨域资源共享 (CORS)](../app-service/overview.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Web 应用或 API 应用。

2. 在打开的应用菜单的 API 下方，选择“API 定义” 。 将“API 定义位置”设置为你的 swagger.json 文件的 URL。

   通常，URL 的格式为：`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![链接到自定义 API 的 Swagger 文档](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. 在“API”下，选择“CORS” 。 将“允许的源”的 CORS 策略设置为 **'*'** （允许所有）。

   此设置允许来自逻辑应用设计器的请求。

   ![允许从逻辑应用设计器向自定义 API 发送请求](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

有关详细信息，请参阅[在 Azure 应用服务中使用 CORS 托管 RESTful API](../app-service/app-service-web-tutorial-rest-api.md)。

## <a name="call-your-custom-api-from-logic-app-workflows"></a>从逻辑应用工作流调用自定义 API

设置 API 定义属性和 CORS 后，应可将自定义 API 的触发器和操作包含在逻辑应用工作流中。 

*  要查看具有 OpenAPI URL 的网站，可在逻辑应用设计器中浏览订阅网站。

*  若要通过指向 Swagger 文档查看可用操作和输入，请使用 [HTTP + Swagger 操作](../connectors/connectors-native-http-swagger.md)。

*  无论调用任何 API（包括不具有或未公开 Swagger 文档的 API），始终可以使用 [HTTP 操作](../connectors/connectors-native-http.md)创建请求。

## <a name="next-steps"></a>后续步骤

* [自定义连接器概述](../logic-apps/custom-connector-overview.md)
