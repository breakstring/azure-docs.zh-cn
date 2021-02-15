---
title: 在 PowerShell 库中使用 Azure 自动化 Runbook 和模块
description: 本文介绍如何在 PowerShell 库中使用来自 Microsoft 和社区的 Runbook 和模块。
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: 590220782a7f43e785cc7885e68eefa99afb7d1d
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049110"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>使用 PowerShell 库中的 Runbook 和模块

无需在 Azure 自动化中创建自己的 Runbook 和模块，即可访问 Microsoft 和社区构建的方案。 你可以从 Azure Automation GitHub 组织的 PowerShell 库和[Python runbook](#use-python-runbooks)获取 PowerShell runbook 和[模块](#modules-in-powershell-gallery)。 还可以通过共享[开发的方案](#add-a-powershell-runbook-to-the-gallery)来为社区做出贡献。

> [!NOTE]
> TechNet 脚本中心正在停用。 Runbook 库中脚本中心的所有 runbook 都已移至我们的 [自动化 GitHub 组织](https://github.com/azureautomation) ，有关详细信息，请参阅 [此处](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)。

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 库中的 Runbook

[PowerShell 库](https://www.powershellgallery.com/packages)提供来自 Microsoft 和社区的各种 Runbook，可将其导入 Azure 自动化。 若要使用 Runbook，可以从库下载 Runbook，也可以直接从库或 Azure 门户中的自动化帐户导入 Runbook。

> [!NOTE]
> PowerShell 库不支持图形 Runbook。

仅可使用 Azure 门户直接从 PowerShell 库导入。 不能使用 PowerShell 执行此功能。

> [!NOTE]
> 应验证从 PowerShell 库获取的任何 Runbook 的内容，在生产环境中安装和运行这些 Runbook 时，请谨慎操作。

## <a name="modules-in-powershell-gallery"></a>PowerShell 库中的模块

PowerShell 模块包含可以在 Runbook 中使用的 cmdlet，并可以在 [PowerShell 库](https://www.powershellgallery.com)中找到可在 Azure 自动化中安装的现有模块。 可以从 Azure 门户启动此库，并将这些模块直接安装到 Azure 自动化中。 也可以下载并手动安装这些模块。

## <a name="common-scenarios-available-in-powershell-gallery"></a>PowerShell 库中提供的常见方案

下面的列表包含一些支持常见方案的 Runbook。 有关由 Azure 自动化团队创建的 Runbook 的完整列表，请参阅 [AzureAutomationTeam 配置文件](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - 从 PowerShell 库导入自动化帐户中所有模块的最新版本。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - 配置 Azure 诊断和日志分析，以接收包含作业状态和作业流的 Azure 自动化日志。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 从 Windows Azure 虚拟机复制远程文件。
   * [ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -将本地文件复制到 Azure 虚拟机。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 门户从 Runbook 库导入 PowerShell Runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 在“过程自动化”下，选择“Runbook 库”。 
3. 选择“源:PowerShell 库”。
4. 找到所需的库项，选择它以查看其详细信息。 在左侧可以输入发布者和类型的其他搜索参数。

   ![浏览库](media/automation-runbook-gallery/browse-gallery.png)

5. 单击 " **查看源项目** " 以查看 [Azure Automation GitHub 组织](https://github.com/azureautomation)中的项目。
6. 若要导入项，请单击项以查看其详细信息，然后单击“导入”。

   ![“导入”按钮](media/automation-runbook-gallery/gallery-item-detail.png)

7. 可选择更改 Runbook 的名称，并单击“确定”导入该 Runbook。
8. Runbook 将出现在自动化帐户的“Runbook”选项卡中。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>将 PowerShell Runbook 添加到库

Microsoft 建议将 Runbook 添加到你认为对其他客户有用的 PowerShell 库中。 PowerShell 库接受 PowerShell 模块和 PowerShell 脚本。 可以通过[将 Runbook 上传到 PowerShell 库](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)来添加 Runbook。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>使用 Azure 门户从模块库导入模块

1. 在 Azure 门户中，打开自动化帐户。
2. 在“共享资源”中选择“模块”，打开模块列表 。
3. 请单击页面顶部的“浏览全部”。

   ![模块库](media/automation-runbook-gallery/modules-blade.png)

4. 在“浏览库”页，可以按以下字段进行搜索：

   * 模块名称
   * Tags
   * 作者
   * Cmdlet/DSC 资源名称

5. 找到感兴趣的模块并选择它以查看其详细信息。

   深入了解特定模块时，可以查看更多信息。 此信息包括返回 PowerShell 库的链接、任何所需的依赖项以及该模块包含的所有 cmdlet 或 DSC 资源。

   ![PowerShell 模块详细信息](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. 若要直接将模块安装到 Azure 自动化中，请单击“导入”。
7. 在“导入”窗格上，可以看到要导入的模块的名称。 如果安装了所有依赖项，“确定”按钮将处于活动状态。 如果缺少依赖项，则需要在导入此模块前导入这些依赖项。
8. 在“导入”窗格上，单击“确定”导入模块。 Azure 自动化将模块导入帐户时，它提取有关该模块和 cmdlet 的元数据。 此操作可能需要几分钟才能完成，因为需要提取每个活动。
9. 将收到正在部署该模块的初始通知；完成此过程后，还会收到另一通知。
10. 导入模块后，可以看到可用的活动。 可以在 Runbook 和 DSC 资源中使用模块资源。

> [!NOTE]
> 仅支持 PowerShell 核心的模块将在 Azure 自动化中不受支持，无法在 Azure 门户中导入，或直接从 PowerShell 库部署。

## <a name="use-python-runbooks"></a>使用 Python Runbook

可在 [Azure 自动化 GitHub 组织](https://github.com/azureautomation)中使用 Python runbook。 当你参与我们的 GitHub 存储库时，请在上传你的 Python3 时，添加标记 **(GitHub 主题) ：** "。

## <a name="request-a-runbook-or-module"></a>请求 Runbook 或模块

可以将请求发送到[用户之声](https://feedback.azure.com/forums/246290-azure-automation/)。  如果编写 Runbook 时需要帮助，或对 PowerShell 存有疑问，请将问题发布到我们的 [Microsoft 问答页面](/answers/topics/azure-automation.html)。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 Runbook，请参阅[在 Azure 自动化中管理 Runbook](manage-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。
