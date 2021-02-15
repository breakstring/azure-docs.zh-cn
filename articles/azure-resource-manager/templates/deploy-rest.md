---
title: 使用 REST API 和模板部署资源
description: 使用 Azure 资源管理器和资源管理器 REST API 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 77192aff9ed4fe33269b5e11891c30e15bc312dd
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028958"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>使用 ARM 模板和 Azure 资源管理器 REST API 来部署资源

本文介绍如何将 Azure 资源管理器 REST API 与 Azure 资源管理器模板（ARM 模板）配合使用，以便将资源部署到 Azure 部署。

可以在请求正文中包含模板或链接到文件。 使用文件时，它可以是本地文件，也可以是通过 URI 提供的外部文件。 如果模板位于存储帐户中，可以限制对该模板的访问，并在部署过程中提供共享访问签名 (SAS) 令牌。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、Azure 订阅、管理组或租户。 你将根据部署范围使用不同的命令。

- 若要部署到资源组，请使用[部署 - 创建](/rest/api/resources/deployments/createorupdate)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

- 若要部署到订阅，请使用[部署 - 在订阅范围内创建](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

- 若要部署到管理组，请使用[部署 - 在管理组范围内创建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  有关管理组级别部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

- 若要部署到租户，请使用[部署 - 在租户范围内创建或更新](/rest/api/resources/deployments/createorupdateattenantscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

本文中的示例使用资源组部署。

## <a name="deploy-with-the-rest-api"></a>使用 REST API 进行部署

1. 设置 [常见参数和标头](/rest/api/azure/)，包括身份验证令牌。

1. 如果要部署到不存在的资源组，请创建该资源组。 提供订阅 ID、新资源组的名称，以及解决方案所需的位置。 有关详细信息，请参阅 [创建资源组](/rest/api/resources/resourcegroups/createorupdate)。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   使用如下所示请求正文：

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. 在部署模板之前，可以预览模板将对环境作出的更改。 使用[假设操作](template-deploy-what-if.md)验证模板是否进行了预期的更改。 假设操作还验证模板是否有错误。

1. 若要部署模板，请在请求 URI 中提供订阅 ID、资源组名称和部署名称。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   在请求正文中，提供指向模板和参数文件的链接。 有关参数文件的详细信息，请参阅[创建资源管理器参数文件](parameter-files.md)。

   请注意， `mode` 设置为 " **增量**"。 若要运行完整部署，请将设置 `mode` 为 **complete**。 使用完整模式时要小心，因为可能会无意中删除不在模板中的资源。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    如果想要记录响应内容或/和请求内容，请在请求中包括 `debugSetting`。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    可以将存储帐户设置为使用共享访问签名 (SAS) 令牌。 有关详细信息，请参阅 [使用共享访问签名委托访问权限](/rest/api/storageservices/delegate-access-with-shared-access-signature)。

    如果需要为参数（如密码）提供敏感值，请将该值添加到密钥保管库。 在部署过程中检索密钥保管库，如前面的示例所示。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

1. 可以将模板和参数包含在请求正文中，而不是链接到模板和参数的文件。 以下示例显示了内联模板和参数的请求正文：

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. 要获取模板部署的状态，请使用[部署 - 获取](/rest/api/resources/deployments/get)。

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>部署名称

可为部署提供一个名称，如 `ExampleDeployment`。

每次运行部署时，一个包含部署名称的条目会添加到资源组的部署历史记录中。 如果运行另一个部署并为其指定了相同的名称，则会将先前的条目替换为当前部署。 如果要在部署历史记录中保持唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，你可以分配一个随机数。 或者，添加日期值。

如果使用相同的部署名称对同一资源组运行并发部署，则仅会完成最后一个部署。 尚未完成的具有相同名称的任何部署都将被最后一个部署所替换。 例如，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将仅部署一个存储帐户。 生成的存储帐户名为 `storage2`。

但是，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；在该部署完成时你又立即运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将有两个存储帐户。 一个名为 `storage1`，另一个名为 `storage2`。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一的名称时，可以并发运行它们而不会发生冲突。 如果你运行一个名为 `newStorage1` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你又运行了另一个名为 `newStorage2` 的部署，它部署了一个名为 `storage2` 的存储帐户，则部署历史记录中将有两个存储帐户和两个条目。

为避免与并发部署冲突并确保部署历史记录中的条目是唯一的，请为每个部署指定唯一的名称。

## <a name="next-steps"></a>后续步骤

- 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](rollback-on-error.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何处理异步 REST 操作，请参阅[跟踪异步 Azure 操作](../management/async-operations.md)。
- 若要详细了解模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
