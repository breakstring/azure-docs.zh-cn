---
title: 使用 Azure 自动化中的源代码管理集成
description: 本文介绍如何将 Azure 自动化源代码管理与其他存储库同步。
services: automation
ms.subservice: process-automation
ms.date: 11/12/2020
ms.topic: conceptual
ms.openlocfilehash: e7a6b6d3e753352820cdcb910dcbfa9362793493
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050764"
---
# <a name="use-source-control-integration"></a>使用源代码管理集成

 Azure 自动化中的源代码管理集成支持源代码管理存储库中的单向同步。 通过源代码管理，可以使用 GitHub 或 Azure Repos 源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。 此功能可以轻松地将已在开发环境中测试过的代码提升到生产自动化帐户。

 使用源代码管理集成可轻松与团队协作、跟踪更改，以及回退到旧版 Runbook。 例如，通过源代码管理可以将源代码管理中的不同分支同步到开发、测试和生产自动化帐户。

## <a name="source-control-types"></a>源代码管理类型

Azure 自动化支持三种类型的源代码管理：

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>先决条件

* 源代码管理存储库（GitHub 或 Azure Repos）
* 一个[运行方式帐户](automation-security-overview.md#run-as-accounts)
* 自动化帐户中[最新的 Azure 模块](automation-update-azure-modules.md)包括 `Az.Accounts` 模块（相当于 `AzureRM.Profile` 的 Az 模块）

> [!NOTE]
> 源代码管理同步作业以自动化帐户用户身份运行，并且按与其他自动化作业相同的费率计费。

## <a name="configure-source-control"></a>配置源代码管理

本部分介绍如何为自动化帐户配置源代码管理。 可以使用 Azure 门户或 PowerShell。

### <a name="configure-source-control-in-azure-portal"></a>在 Azure 门户中配置源代码管理

使用此过程通过 Azure 门户配置源代码管理。

1. 在自动化帐户中，选择“源代码管理”，然后单击“添加” 。

    ![选择“源代码管理”](./media/source-control-integration/select-source-control.png)

2. 选择“源代码管理类型”，然后单击“身份验证” 。

3. 随即将打开一个浏览器窗口，并提示你登录。 根据提示完成身份验证。

4. 在“源代码管理摘要”页上，使用字段填写下面定义的源代码管理属性。 完成后单击“保存”。

    |properties  |说明  |
    |---------|---------|
    |源代码管理名称     | 源代码管理的友好名称。 该名称只能包含字母和数字。        |
    |源代码管理类型     | 源代码管理机制的类型。 可用选项包括：</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |存储库     | 存储库或项目的名称。 检索前 200 个存储库。 要搜索存储库，请在字段中键入名称，然后单击“在 GitHub 上搜索”。|
    |分支     | 要从源文件中提取的分支。 分支目标确定不适用于 TFVC 源代码管理类型。          |
    |文件夹路径     | 包含要同步的 Runbook 的文件夹，例如 /Runbook。 仅同步指定文件夹中的 Runbook。 不支持递归。        |
    |自动同步<sup>1</sup>     | 在源代码管理存储库中提交时打开或关闭自动同步的设置。        |
    |发布 Runbook     | 如果从源代码管理同步后自动发布 Runbook，则设置为“开启”，否则设为“关闭”。           |
    |说明     | 指定有关源代码管理的其他详细信息的文本。        |

    <sup>1</sup> 要在配置与 Azure Repos 的源代码管理集成时启用自动同步，你必须是项目管理员。

   ![源代码管理摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 源代码管理存储库的登录名可能与 Azure 门户的登录不同。 配置源代码管理时，请确保使用正确的源代码管理存储库的帐户登录。 如果有疑问，请在浏览器中打开新的选项卡并从 dev.azure.com、visualstudio.com 或 github.com 中注销，然后尝试连接源代码管理  。

### <a name="configure-source-control-in-powershell"></a>在 PowerShell 中配置源代码管理

还可以使用 PowerShell 在 Azure 自动化中配置源代码管理。 要使用 PowerShell cmdlet 执行此操作，需要个人访问令牌 (PAT)。 使用 [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) cmdlet 创建源代码管理连接。 此 cmdlet 需要 PAT 的安全字符串。 要了解如何创建安全字符串，请参阅 [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)。

以下小节说明如何使用 PowerShell 创建适用于 GitHub、Azure Repos (Git) 和 Azure Repos (TFVC) 的源代码管理连接。

#### <a name="create-source-control-connection-for-github"></a>创建适用于 GitHub 的源代码管理连接

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>创建适合于 Azure Repos (Git) 的源代码管理连接

> [!NOTE]
> Azure Repos (Git) 使用的 URL 属于早期格式，只可访问 dev.azure.com，但不可访问 visualstudio.com 。 早期的 URL 格式 `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` 已被弃用，但仍受支持。 首选新格式。


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>创建适用于 Azure Repos (TFVC) 的源代码管理连接

> [!NOTE]
> Azure Repos (TFVC) 使用的 URL 属于早期格式，只可访问 dev.azure.com，但不可访问 visualstudio.com 。 早期的 URL 格式 `https://<accountname>.visualstudio.com/<projectname>/_versionControl` 已被弃用，但仍受支持。 首选新格式。

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>个人访问令牌 (PAT) 权限

源代码管理需要一些 PAT 的最低权限。 以下小节包含 GitHub 和 Azure Repos 所需的最低权限。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub 的最低 PAT 权限

下表定义了 GitHub 所需的最低 PAT 权限。 如需详细了解如何在 GitHub 中创建 PAT，请参阅[创建命令行的个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|范围  |说明  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 访问提交状态         |
|`repo_deployment`      | 访问部署状态         |
|`public_repo`     | 访问公共存储库         |
|`repo:invite` | 访问存储库邀请 |
|`security_events` | 读取和写入安全事件 |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 写入存储库挂钩         |
|`read:repo_hook`|读取存储库挂钩|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos 的最低 PAT 权限

下表定义了 Azure Repos 所需的最低 PAT 权限。 如需详细了解如何在 Azure Repos 中创建 PAT，请参阅[使用个人访问令牌进行身份验证访问](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

| 范围  |  访问类型  |
|---------| ----------|
| `Code`      | 读取  |
| `Project and team` | 读取 |
| `Identity` | 读取     |
| `User profile` | 读取     |
| `Work items` | 读取    |
| `Service connections` | 读取、查询、管理<sup>1</sup>    |

<sup>1</sup>只有在启用了自动同步时才需要 `Service connections` 权限。

## <a name="synchronize-with-source-control"></a>与源代码管理同步

请按照以下步骤与源代码管理同步。

1. 请从“源代码管理”页面上的表中选择源。

2. 单击“开始同步”以开始同步过程。

3. 单击“同步作业”选项卡，查看当前同步作业或之前的同步作业的状态。

4. 在“源代码管理”下拉列表中，选择一个源代码管理机制。

    ![同步状态](./media/source-control-integration/sync-status.png)

5. 单击某个作业可以查看作业输出。 以下示例是源代码管理同步作业的输出。

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. 通过在“源代码管理同步作业摘要”页上选择“所有日志”，可以获取附加日志记录。 这些附加日志条目有助于你解决使用源代码管理时可能出现的问题。

## <a name="disconnect-source-control"></a>断开连接源代码管理

断开与源代码管理存储库的连接：

1. 在自动化帐户中的“帐户设置”下打开源代码管理 。

2. 选择要删除的源代码管理机制。

3. 在“源代码管理摘要”页面上，单击“删除”。

## <a name="handle-encoding-issues"></a>处理编码问题

如果多人使用不同的编辑器在源代码管理存储库中编辑 Runbook，则可能发生编码问题。 如需详细了解此情况，请参阅[编码问题的常见原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)。

## <a name="update-the-pat"></a>更新 PAT

目前，不能使用 Azure 门户在源代码管理中更新 PAT。 当 PAT 过期或吊销时，可以通过以下方式之一使用新的访问令牌更新源代码管理：

* 使用 [REST API](/rest/api/automation/sourcecontrol/update)。
* 使用 [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet。

## <a name="next-steps"></a>后续步骤

* 若要在 Azure 自动化中集成源代码管理，请参阅 [Azure 自动化：Azure 自动化中的源代码管理集成](https://azure.microsoft.com/blog/azure-automation-source-control-13/)。  
* 若要将 runbook 源代码管理与 Visual Studio Codespaces 集成，请参阅 [Azure Automation：使用 Visual Studio 集成 Runbook 源代码管理 Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)。
