---
title: 教程 - 将标记添加到模板中的资源
description: 将标记添加到在 Azure 资源管理器模板（ARM 模板）中部署的资源。 可以通过标记对资源进行逻辑组织。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106996"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>教程：在 ARM 模板中添加标记

本教程介绍如何将标记添加到 Azure 资源管理器模板（ARM 模板）中的资源。 可以通过[标记](../management/tag-resources.md)对资源进行逻辑组织。 标记值显示在成本报告中。 完成本教程需要 **8 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关快速入门模板的教程](template-tutorial-quickstart-template.md)，但这不是必需的。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

以前的模板部署了存储帐户、应用服务计划和 Web 应用。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

部署这些资源以后，可能需要跟踪成本并找到属于某个类别的资源。 可以通过添加标记来解决这些问题。

## <a name="add-tags"></a>添加标记

可以通过对资源进行标记来添加有助于标识其用途的值。 例如，可以通过添加标记来列出环境和项目。 可以通过添加标记来确定某个成本中心或拥有该资源的团队。 添加对组织来说有意义的任何值。

以下示例突出显示了对模板所做的更改。 复制整个文件，将模板替换为该文件的内容。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>部署模板

可以部署模板并查看结果了。

如果尚未创建资源组，请参阅[创建资源组](template-tutorial-create-first-template.md#create-resource-group)。 此示例假设已根据[第一篇教程](template-tutorial-create-first-template.md#deploy-template)中所述，将 `templateFile` 变量设置为模板文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 选择一项资源，例如存储帐户资源。 可以看到，它现在有标记。

   ![显示标记](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们向资源添加了标记。 下一教程介绍如何使用参数文件，以便简化将值传入模板的过程。

> [!div class="nextstepaction"]
> [使用参数文件](template-tutorial-use-parameter-file.md)
