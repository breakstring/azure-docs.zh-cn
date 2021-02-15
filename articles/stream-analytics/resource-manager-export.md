---
title: 导出 Azure 流分析作业 Azure 资源管理器模板
description: 本文介绍如何导出 Azure 流分析作业的 Azure 资源管理器模板。
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015413"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>导出 Azure 流分析作业 Azure 资源管理器模板

[Azure 资源管理器模板](../azure-resource-manager/templates/overview.md) 使你可以实现基础结构即代码。 模板是一种 JavaScript 对象表示法 (JSON) 文件，用于定义资源的基础结构和配置。 指定要部署的资源以及这些资源的属性。

可以通过导出 Azure 资源管理器模板来重新部署 Azure 流分析作业。

## <a name="open-a-job-in-vs-code"></a>在 VS Code 中打开作业

导出模板之前，必须先在 Visual Studio Code 中打开现有的流分析作业。 

若要将作业导出到本地项目，请在 " **流分析资源管理器** " 的 Azure 门户中找到要导出的作业。 在 " **查询** " 页上，选择 " **在 Visual Studio 中打开**"。 然后选择 **Visual Studio Code**。

![Visual Studio Code 中打开流分析作业](./media/resource-manager-export/open-job-vs-code.png)

有关使用 Visual Studio Code 管理流分析作业的详细信息，请参阅 [Visual Studio Code 快速入门](quick-create-visual-studio-code.md)。

## <a name="compile-the-script"></a>编译脚本 

下一步是将作业脚本编译为 Azure 资源管理器模板。 在编译脚本之前，请确保作业至少配置了一个输入和一个输出。 如果未配置任何输入或输出，则需要首先配置输入和输出。

1. 在 Visual Studio Code 中，导航到作业的 *script.asaql* 文件。

   ![Visual Studio Code 中的 script.asaql 文件](./media/resource-manager-export/transformation-asaql.png)

1. 右键单击 *script.asaql* 文件，然后从菜单中选择 " **ASA：编译脚本** "。

1. 请注意， **部署** 文件夹出现在流分析作业工作区中。

1. 浏览文件 *上的JobTemplate.js* ，它是用于部署的 Azure 资源管理模板。

## <a name="complete-the-parameters-file"></a>完成参数文件

接下来，完成 Azure 资源管理模板参数文件。

1. 在 Visual Studio Code 中打开位于流分析作业工作区的 "**部署**" 文件夹中的 *JobTemplate.parameters.js* 文件。

1. 请注意，输入和输出密钥为 null。 将空值替换为输入和输出资源的实际访问密钥。

1. 保存参数文件。

## <a name="deploy-using-templates"></a>使用模板部署

你已准备好使用上一节中生成的 Azure 资源管理器模板部署 Azure 流分析作业。

在 PowerShell 窗口中运行以下命令。 请确保将 *ResourceGroupName*、 *TemplateFile* 和 *TemplateParameterFile* 与实际资源组名称 reaplce，并将完整的文件路径放到 *JobTemplate.js上* 的，并 *JobTemplate.parameters.js* 作业工作区的 " **部署" 文件夹** 中的文件。

如果尚未配置 Azure PowerShell，请按照 [安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)中的步骤进行操作。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>后续步骤

* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)

* [利用 Visual Studio Code (预览版了解 Azure 流分析作业) ](visual-studio-code-explore-jobs.md)