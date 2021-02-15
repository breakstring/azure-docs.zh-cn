---
title: 如何使用非交互式登录保护单页面应用程序
titleSuffix: Azure Maps
description: 如何使用非交互式 Azure 基于角色的访问控制配置单个页面应用程序 (Azure RBAC) 和 Azure Maps Web SDK。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 3b647a02731a4fa404aab4bb648853eea766d92e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684066"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>如何使用非交互式登录保护单页面应用程序

以下指南适用于使用 Azure Active Directory (Azure AD) 的应用程序，以便在用户无法登录 Azure Maps 时提供访问令牌，以 Azure AD 应用程序。 此流需要托管 web 服务，该服务必须受保护才能仅由单页 web 应用程序访问。 有多个实现可实现 Azure AD 的身份验证。 本指南利用产品和 Azure 函数获取访问令牌。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure maps 可支持用户登录/交互式流中的访问令牌。 交互式流支持更受限制的访问吊销和机密管理的范围。

## <a name="create-azure-function"></a>创建 Azure 函数

创建安全的 web 服务应用程序，该应用程序负责 Azure AD 的身份验证。 

1. 在 Azure 门户中创建函数。 有关详细信息，请参阅 [Create Azure Function](../azure-functions/functions-get-started.md)。

2. 在 Azure 函数上配置 CORS 策略，使其可供单个页面 web 应用程序访问。 这将使浏览器客户端安全地成为你的 web 应用程序的允许来源。 请参阅 [添加 CORS 功能](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)。

3. 在 Azure 函数中[添加系统分配的标识](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)，以允许创建服务主体，以便对 Azure AD 进行身份验证。  

4. 向 Azure Maps 帐户授予系统分配的标识的基于角色的访问权限。 有关详细信息，请参阅 [授予基于角色的访问权限](#grant-role-based-access) 。

5. 编写 Azure 函数的代码，以使用系统分配的标识和支持的机制之一或 REST 协议获取 Azure Maps 访问令牌。 请参阅 [获取 Azure 资源的令牌](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    示例 REST 协议示例：

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    示例响应：

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. 配置 Azure function HttpTrigger 的安全性

   * [创建函数访问密钥](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * 在生产环境中保护 Azure 功能的[HTTP 终结点](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)。
   
7. 配置 web 应用程序 Azure Maps Web SDK。 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>授予基于角色的访问权限

可以通过将系统分配的标识分配给一个或多个 Azure 角色定义，将 *基于角色的访问控制授予 AZURE RBAC) access (* 。 若要查看可用于 Azure Maps 的 Azure 角色定义，请 **访问 (IAM) 的 "访问控制**"。 选择 " **角色**"，然后搜索以 *Azure Maps* 开头的角色。

1. 中转到你的 **Azure Maps 帐户**。 **(IAM)**  >  **角色分配** 选择 "访问控制"。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure RBAC 授予访问权限](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 " **角色分配** " 选项卡上的 " **角色**" 下，选择内置 Azure Maps 角色定义，如 **Azure Maps 数据读取器** 或 **Azure Maps 数据参与者**。 在 " **分配访问权限**" 下，选择 **Function App**。 按名称选择主体。 再选择“保存”。

   * 有关 [添加或删除角色分配](../role-based-access-control/role-assignments-portal.md)的详细信息，请参阅。

> [!WARNING]
> Azure Maps 内置角色定义提供对许多 Azure Maps REST Api 的一种非常大的授权访问权限。 若要限制对 Api 的访问权限，请参阅 [创建自定义角色定义和将系统分配的标识分配](../role-based-access-control/custom-roles.md) 给自定义角色定义。 这将允许应用程序访问 Azure Maps 所需的最小特权。

## <a name="next-steps"></a>后续步骤

进一步了解单页面应用程序方案：
> [!div class="nextstepaction"]
> [单页应用程序](../active-directory/develop/scenario-spa-overview.md)

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索其他演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)