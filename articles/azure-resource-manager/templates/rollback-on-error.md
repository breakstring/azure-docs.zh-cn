---
title: 出错时回退以成功部署
description: 指定失败的部署应回退到成功的部署。
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492206"
---
# <a name="rollback-on-error-to-successful-deployment"></a>出错时回退以成功部署

部署失败时，可以自动重新部署部署历史记录中先前成功的部署。 如果基础结构部署存在一个已知良好的状态，并且你希望还原到此状态，则此功能非常有用。 你可以指定先前的特定部署或上次成功的部署。

> [!IMPORTANT]
> 此功能通过重新部署以前的部署来回滚失败的部署。 此结果可能不同于撤消失败的部署所需的结果。 确保你了解如何重新部署以前的部署。

## <a name="considerations-for-redeploying"></a>重新部署注意事项

使用此功能之前，请考虑以下有关如何处理重新部署的详细信息：

- 以前的部署是使用 [完整模式](./deployment-modes.md#complete-mode)运行的，即使在之前的部署过程中使用了 [增量模式](./deployment-modes.md#incremental-mode) 也是如此。 如果先前的部署使用了增量，在完整模式下重新部署可能会产生意外的结果。 完整模式意味着将删除以前的部署中未包含的所有资源。 指定一个以前的部署，它表示要在资源组中存在的所有资源及其状态。 有关详细信息，请参阅 [部署模式](./deployment-modes.md)。
- 重新部署使用与以前运行它时相同的参数以相同的方式运行。 无法更改参数。
- 重新部署只会影响资源，而不会影响任何数据更改。
- 此功能只能用于资源组部署。 它不支持订阅、管理组或租户级别的部署。 有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](./deploy-to-subscription.md)。
- 只能将此选项用于根级别部署。 从嵌套模板进行的部署不可用于重新部署。

若要使用此选项，部署历史记录中的部署必须具有唯一名称。 只有唯一的名称才能识别特定的部署。 如果没有唯一名称，则失败的部署可能会覆盖历史记录中成功的部署。

如果指定的部署在部署历史记录中不存在，则回滚将返回错误。

## <a name="powershell"></a>PowerShell

若要重新部署最后一个成功的部署，请将 `-RollbackToLastDeployment` 参数添加为标志。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

若要重新部署某个特定部署，请使用 `-RollBackDeploymentName` 参数并提供部署名称。 指定的部署必须已成功。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

若要重新部署最后一个成功的部署，请将 `--rollback-on-error` 参数添加为标志。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

若要重新部署某个特定部署，请使用 `--rollback-on-error` 参数并提供部署名称。 指定的部署必须已成功。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

若要在当前部署失败的情况下，重新部署上一个成功部署，请使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

若要在当前部署失败的情况下，重新部署特定部署，请使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

指定的部署必须已成功。

## <a name="next-steps"></a>后续步骤

- 若要了解完整和增量模式，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 Azure Resource Manager 模板的结构和语法](template-syntax.md)。
