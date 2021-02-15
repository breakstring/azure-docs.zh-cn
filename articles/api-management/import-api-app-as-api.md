---
title: 使用 Azure 门户将 API 应用导入为 API | Microsoft Docs
description: 本文演示如何使用 API 管理 (APIM) 将 API 应用导入为 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994734"
---
# <a name="import-an-api-app-as-an-api"></a>将 API 应用导入为 API

本文演示如何将 API 应用导入为 API。 此外，介绍如何测试 APIM API。

在本文中，学习如何：

> [!div class="checklist"]
> * 将 API 应用导入为 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ 确保订阅中已有 API 应用。 有关详细信息，请参阅[应用服务文档](../app-service/index.yml)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>导入和发布后端 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”。
2. 从“添加新的 API”列表中选择“API 应用” 。

    ![API 应用](./media/import-api-app-as-api/api-app.png)
3. 按“浏览”查看订阅中的 API 应用列表。
4. 选择应用。 APIM 找到与所选应用关联的 swagger 后，将其提取并导入。 

    如果 APIM 未找到 swagger，则会将 API 公开为“传递”API。 
5. 添加 API URL 后缀。 后缀是用于在该 APIM 实例中标识此特定 API 的名称。 它在此 APIM 实例中必须唯一。
6. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品。  如果想要发布 API 并使其对开发人员可用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。

    产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。

    默认情况下，每个 API 管理实例附带两个示例产品：

    * **入门**
    * **不受限制**   
7. 输入其他 API 设置。 可以在创建过程中设置这些值，也可以稍后转到“设置”选项卡来配置这些值。在[导入和发布第一个 API](import-and-publish.md#import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
8. 选择“创建”。

## <a name="test-the-new-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。  

1. 选择上一步中创建的 API。
2. 按“测试”选项卡。
3. 选择某个操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，用于提供和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。 
1. 按“发送”。

    后端以“200 正常”和某些数据做出响应。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
