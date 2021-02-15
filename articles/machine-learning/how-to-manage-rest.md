---
title: 使用 REST 管理 ML 资源
titleSuffix: Azure Machine Learning
description: 如何使用 REST Api 创建、运行和删除 Azure 机器学习资源，如工作区或注册模型。
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 09a0580adbe6d51e4de811a57ee17203d65a2435
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316899"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>使用 REST 创建、运行和删除 Azure ML 资源



可通过多种方式管理 Azure ML 资源。 可以使用[门户](https://portal.azure.com/)、[命令行接口](/cli/azure/?preserve-view=true&view=azure-cli-latest)或 [Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)。 或者，可以选择 REST API。 REST API 使用 HTTP 谓词以标准方式创建、检索、更新和删除资源。 REST API 适用于可发出 HTTP 请求的任何语言或工具。 REST 具有简单的结构，因此它往往是适合脚本编写环境和 MLOps 自动化的良好选择。 

在本文中，学习如何：

> [!div class="checklist"]
> * 检索授权令牌
> * 使用服务主体身份验证创建格式正确的 REST 请求
> * 使用 GET 请求检索有关 Azure ML 分层资源的信息
> * 使用 PUT 与 POST 请求创建和修改资源
> * 使用 DELETE 请求清理资源 
> * 使用基于密钥的授权来为部署的模型评分

## <a name="prerequisites"></a>先决条件

- 你对其拥有管理权限的 **Azure 订阅** 。 如果没有此类订阅，请尝试注册[免费或付费的个人订阅](https://aka.ms/AMLFree)
- 一个 [Azure 机器学习工作区](./how-to-manage-workspace.md)
- 管理 REST 请求使用服务主体身份验证。 遵循[为 Azure 机器学习资源和工作流设置身份验证](./how-to-setup-authentication.md#service-principal-authentication)中的步骤在工作区中创建服务主体
- **curl** 实用工具。 在 [适用于 Linux 的 Windows 子系统](/windows/wsl/install-win10)或任何 UNIX 分发版中均已提供了 **curl** 程序。 在 PowerShell 中， **curl** 是 **Invoke-WebRequest** 的别名，并且 `curl -d "key=val" -X POST uri` 变成了 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`。 

## <a name="retrieve-a-service-principal-authentication-token"></a>检索服务主体身份验证令牌

使用 OAuth2 隐式流对管理 REST 请求进行身份验证。 此身份验证流使用订阅服务主体提供的令牌。 若要检索此令牌，需要提供：

- 租户 ID（标识订阅所属的组织）
- 客户端 ID（将与创建的令牌相关联）
- 客户端机密（应予以保护）

应从创建服务主体的响应中获取这些值。 [为 Azure 机器学习资源和工作流设置身份验证](./how-to-setup-authentication.md#service-principal-authentication)一文介绍了如何获取这些值。 如果使用公司订阅，则可能无权创建服务主体。 在这种情况下，应使用[免费或付费的个人订阅](https://aka.ms/AMLFree)。

若要检索令牌，请执行以下操作：

1. 打开终端窗口
1. 在命令行中输入以下代码
1. 请将 `{your-tenant-id}`、`{your-client-id}` 和 `{your-client-secret}` 替换为自己的值。 在整篇文章中，以大括号括住的字符串是变量，必须将其替换为自己的适当值。
1. 运行命令

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

响应中应会提供有效期为一小时的访问令牌：

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

请记下该令牌，因为以后需要用它来对所有后续管理请求进行身份验证。 为此，可在所有请求中设置一个授权标头：

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

请注意，该值以字符串“Bearer ”开头（其中包含一个空格，你需要在该空格后添加令牌）。

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>获取与订阅关联的资源组列表

若要检索与订阅关联的资源组列表，请运行：

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

整个 Azure 上发布了许多的 REST API。 每家服务提供商会按照自己的步调更新其 API，但这样做不会破坏现有的程序。 服务提供商使用 `api-version` 参数来确保兼容性。 `api-version` 参数因服务而异。 例如，对于机器学习服务，当前的 API 版本是 `2019-11-01`。 对于存储帐户，当前版本是 `2019-06-01`。 对于 Key Vault，当前版本是 `2019-09-01`。 所有 REST 调用应将 `api-version` 参数设置为预期值。 尽管 API 在持续演进，但你仍可以依赖于指定版本的语法和语义。 如果在不使用 `api-version` 参数的情况下向提供商发送请求，则响应将包含可人工读取的受支持值列表。 

上述调用将生成以下格式的压缩 JSON 响应： 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>向下钻取到工作区及其资源

若要检索资源组中的工作区集，请运行以下命令（请替换 `{your-subscription-id}`、`{your-resource-group}` 和 `{your-access-token}`）： 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同样，你会收到一个 JSON 列表，这次，该 JSON 列表包含一个列表，其中的每个项详细提供了有关工作区的信息：

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

若要处理工作区中的资源，需从常规 **management.azure.com** 服务器切换到特定于工作区位置的 REST API 服务器。 请记下上述 JSON 响应中 `discoveryUrl` 密钥的值。 如果 GET 该 URL，会收到如下响应：

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api` 响应的值是要用于其他请求的服务器的 URL。 例如，若要列出试验，请发送以下命令。 请将 `regional-api-server` 替换为 `api` 响应的值（例如 `centralus.api.azureml.ms`）。 还像往常一样替换 `your-subscription-id`、`your-resource-group`、`your-workspace-name` 和 `your-access-token`：

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同样，若要检索工作区中已注册的模型，请发送：

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

请注意，若要列出试验，路径需以 `history/v1.0` 开头；若要列出模型，路径需以 `modelmanagement/v1.0` 开头。 REST API 划分为多个操作组，每个操作组具有不同的路径。 

|区域|`Path`|
|-|-|
|Artifacts|/rest/api/azureml|
|数据存储|/azure/machine-learning/how-to-access-data|
|超参数优化|hyperdrive/v1.0/|
|模型|modelmanagement/v1.0/|
|运行历史记录|execution/v1.0/ and history/v1.0/|

可以使用以下常规模式浏览 REST API：

|URL 组成部分|示例|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/{your-subscription-id}/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>使用 PUT 与 POST 请求创建和修改资源

除了使用 GET 谓词检索资源以外，REST API 还支持创建训练、部署和监视 ML 解决方案所需的所有资源。 

训练和运行 ML 模型需要计算资源。 可使用以下代码列出工作区的计算资源： 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

若要创建或覆盖命名计算资源，需使用 PUT 请求。 在以下代码中，除了像平时一样替换 `your-subscription-id`、`your-resource-group`、`your-workspace-name` 和 `your-access-token` 以外，还需要替换 `your-compute-name`，以及 `location`、`vmSize`、`vmPriority`、`scaleSettings`、`adminUserName` 和 `adminUserPassword` 的值。 根据[机器学习计算 - 创建或更新 SDK 参考](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)中的明确说明，以下命令将创建一个专用的单节点 Standard_D1（基本 CPU 计算资源），该资源在 30 分钟后将会纵向缩减：

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> 在 Windows 终端中，可能需要在发送 JSON 数据时对双引号符号进行转义。 也就是说，诸如 `"location"` 之类的文本将变成 `\"location\"`。 

成功的请求将获取 `201 Created` 响应，但请注意，此响应仅表示预配过程已开始。 需要通过轮询（或使用门户）来确认它是否成功完成。

### <a name="create-an-experimental-run"></a>创建试验运行

若要在试验中启动运行，需要一个包含训练脚本和相关文件的 zip 文件夹，以及一个运行定义 JSON 文件。 该 zip 文件夹必须在其根目录中包含 Python 入口文件。 例如，将如下所示的普通 Python 程序压缩到名为 **train.zip** 的文件夹中。

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

将下一个代码片段保存为 **definition.json** 。 确认“Script”值与刚刚压缩的 Python 文件的名称相匹配。 确认“Target”值与可用计算资源的名称相匹配。 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

使用 `multipart/form-data` 内容将这些文件发布到服务器：

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

成功的 POST 请求将生成 `200 OK` 状态，以及包含所创建运行的标识符的响应正文：

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

可以使用现在应很熟悉的 RESTful 模式监视运行：

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>删除不再需要的资源

某些（但不是所有）资源支持 DELETE 谓词。 在删除用例中提交到 REST API 之前，请查看 [API 参考](/rest/api/azureml/)。 例如，若要删除某个模型，可以使用：

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>使用 REST 为部署的模型评分

尽管可以部署一个模型以使其使用服务主体进行身份验证，但大多数面向客户端的部署使用基于密钥的身份验证。 可以在工作室的部署页中的“终结点”选项卡上找到相应的密钥。 同一位置会显示终结点的评分 URI。 必须将模型的输入建模为名为 `data` 的 JSON 数组：

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>使用 REST 创建工作区 

每个 Azure ML 工作区依赖于其他四个 Azure 资源：启用了管理的容器注册表、密钥保管库、Application Insights 资源和存储帐户。 只有存在这些资源，才能创建工作区。 有关创建每个此类资源的详细信息，请查阅 REST API 参考。

若要创建工作区，请在 `management.azure.com` 中放置 (PUT) 如下所示的调用。 尽管此调用要求设置大量的变量，但在结构上，它与本文所述的其他调用相同。 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

应会收到 `202 Accepted` 响应，并在返回的标头中收到 `Location` URI。 可以获取 (GET) 此 URI 以获取有关部署的信息，包括某个依赖资源出现问题（例如，你忘记了在容器注册表中启用管理员访问权限）时提供的有用调试信息。 

## <a name="troubleshooting"></a>故障排除

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

- 浏览完整的 [AzureML REST API 参考](/rest/api/azureml/)。
- 了解如何[使用设计器预测汽车价格](./tutorial-designer-automobile-price-train-score.md)。
- [使用 Jupyter 笔记本探索 Azure 机器学习](..//machine-learning/samples-notebooks.md)。