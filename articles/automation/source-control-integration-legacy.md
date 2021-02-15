---
title: 在 Azure 自动化中使用源代码管理集成 - 传统
description: 本文介绍了如何使用源代码管理集成。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591867"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>在 Azure 自动化中使用源代码管理集成 - 传统

> [!NOTE]
> 有一种新的源代码管理体验。 若要了解有关新体验的更多信息，请参阅[源代码管理（预览）](source-control-integration.md)。

源代码管理集成可让你将自动化帐户中的 Runbook 关联到 GitHub 源代码管理存储库。 使用源代码管理可轻松与团队协作、跟踪更改，以及回退到旧版 Runbook。 例如，源代码管理可让你将源代码管理中的不同分支同步到开发、测试或生产自动化帐户，以轻松地将已在开发环境中测试过的代码提升到生产自动化帐户。

源代码管理可让你将代码从 Azure 自动化推送到源代码管理，或将 Runbook 从源代码管理拉取到 Azure 自动化。 本文介绍如何在 Azure 自动化环境中设置源代码管理。 我们将首先配置 Azure 自动化以访问 GitHub 存储库，并演练可使用源代码管理集成完成的不同操作。 

> [!NOTE]
> 源代码管理支持拉取和推送 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 以及 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)。 目前不支持[图形 Runbook](automation-runbook-types.md#graphical-runbooks)。

## <a name="configure-source-control"></a>配置源代码管理

为自动化帐户设置源代码管理时，需要执行两个简单的步骤；如果已有 GitHub 帐户，则只需要执行一个步骤。 

### <a name="create-a-github-repository"></a>创建 GitHub 存储库

如果已有想要链接到 Azure 自动化的 GitHub 帐户和存储库，请登录到现有帐户并从下面的步骤 2 开始。 否则，请导航到 [GitHub](https://github.com/)，注册新帐户并[创建新的存储库](https://help.github.com/articles/create-a-repo/)。

### <a name="set-up-source-control"></a>设置源代码管理

1. 在 Azure 门户的“自动化帐户”页上，单击“帐户设置”下的“源代码管理” 。

2. “源代码管理”页面随即打开，可以在其中配置 GitHub 帐户详细信息。 下面是要配置的参数的列表：  

   | **Parameter** | **说明** |
   |:--- |:--- |
   | 选择源 |选择源。 目前仅支持 **GitHub**。 |
   | 授权 |单击“授权”按钮，授予 GitHub 存储库的 Azure 自动化访问权限。 如果已在不同的窗口中登录 GitHub 帐户，将使用该帐户的凭据。 成功授权之后，页面上的“授权属性”下将显示你的 GitHub 用户名。 |
   | 选择存储库 |从可用存储库列表中选择 GitHub 存储库。 |
   | 选择分支 |从可用分支列表中选择分支。 如果尚未创建任何分支，则只显示 **main** 分支。 |
   | Runbook 文件夹路径 |Runbook 文件夹路径可指定 GitHub 存储库中的路径，以便从中推送或提取代码。 必须以 **/foldername/subfoldername** 格式输入路径。 只有 Runbook 文件夹路径中的 Runbook 才同步到自动化帐户。 Runbook 文件夹路径的子文件夹中的 Runbook **不会** 同步。 使用 **/** 来同步存储库下的所有 Runbook。 |
3. 例如，如果有名为 **PowerShellScripts** 的存储库，其中包含名为 **RootFolder** 的文件夹，而该文件夹包含名为 **SubFolder** 的文件夹。 那么，可以使用以下字符串来同步每个文件夹级别：

   1. 若要从 **存储库** 同步 Runbook，则 Runbook 文件夹路径为 **/** 。
   2. 若要从 **RootFolder** 同步 Runbook，则 Runbook 文件夹路径为 **/RootFolder**。
   3. 若要从 **SubFolder** 同步 Runbook，则 Runbook 文件夹路径为 **/RootFolder/SubFolder**。
4. 配置参数后，它们会显示在“设置源代码管理”页面上。  

    ![显示设置的“源代码管理”页](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. 单击“确定”后，随即会针对你的自动化帐户配置源代码管理集成，并且应会使用你的 GitHub 信息更新该集成。 现在，可以单击此部分来查看所有源代码管理同步作业历史记录。  

    ![当前配置的源代码管理配置的值](media/source-control-integration-legacy/automation-RepoValues.png)
6. 设置源代码管理后，将在自动化帐户中创建两个[变量资产](./shared-resources/variables.md)。 此外，会将一个已授权的应用程序添加到你的 GitHub 帐户。

   * 变量 **Microsoft.Azure.Automation.SourceControl.Connection** 包含连接字符串的值，如下所示。  

     | **Parameter** | **值** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |字符串 |
     | `Value` |{"Branch":\<*Your branch name*>,"RunbookFolderPath":\<*Runbook folder path*>,"ProviderType":\<*has a value 1 for GitHub*>,"Repository":\<*Name of your repository*>,"Username":\<*Your GitHub user name*>} |

   * 变量 **Microsoft.Azure.Automation.SourceControl.OAuthToken** 包含 OAuthToken 的安全加密值。  

     |**Parameter**            |**值** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![显示了源代码管理变量的窗口](media/source-control-integration-legacy/automation-Variables.png)  

   * **自动化源代码管理** 已作为已授权的应用程序添加到 GitHub 帐户。 若要查看应用程序，请从 GitHub 主页导航到“配置文件” > “设置” > “应用程序”。 此应用程序可让 Azure 自动化将 GitHub 存储库同步到自动化帐户。  

     ![GitHub 中的应用程序设置](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>在自动化中使用源代码管理

Runbook 签入可让你将对 Azure 自动化中的 Runbook 所做的更改推送到源代码管理存储库。 下面是签入 Runbook 的步骤：

1. 从自动化帐户中[创建新的文本 Runbook](./learn/automation-tutorial-runbook-textual.md)，或[编辑现有的文本 Runbook](automation-edit-textual-runbook.md)。 此 Runbook 可以是 PowerShell 工作流或 PowerShell 脚本 Runbook。  
2. 编辑 Runbook 之后，将其保存，然后单击“编辑”页上的“签入”。  

    ![显示了“签入 GitHub”按钮的窗口](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > 从 Azure 自动化签入会覆盖源代码管理中当前存在的代码。 用于签入的 Git 等效命令行指令为 **git add + git commit + git push**  

3. 单击“签入”时，会通过一条确认消息进行提示，请单击“是”以继续操作。   

    ![一个要求确认签入到源代码管理的对话框](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. 签入操作会启动源代码管理 runbook：**Sync-MicrosoftAzureAutomationAccountToGitHubV1**。 此 Runbook 将连接到 GitHub 并将 Azure 自动化中的更改推送到存储库。 若要查看已签入的作业历史记录，请返回到“源代码管理集成”选项卡，单击以打开“存储库同步”页面。 此页将显示你的所有源代码管理作业。 选择要查看的作业，并单击以查看详细信息。  

    ![显示了同步作业结果的窗口](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > 源代码管理 Runbook 是特殊的自动化 Runbook，无法查看或编辑。 虽然它们不显示在 Runbook 列表上，但你可以看到显示在作业列表中的同步作业。

5. 修改后的 Runbook 的名称将发送为已签入 Runbook 的输入参数。 可以通过在“存储库同步”页中展开 Runbook 来[查看作业详细信息](automation-runbook-execution.md#job-statuses)。  

    ![显示了同步作业的输入的窗口](media/source-control-integration-legacy/automation-CheckinInput.png)
6. 在作业完成时刷新 GitHub 存储库可以查看更改。  存储库中应有一个提交项，其提交消息为：“已在 Azure 自动化中更新 *Runbook 名称*。”  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>将源代码管理中的 Runbook 同步到 Azure 自动化

使用“存储库同步”页面上的“同步”按钮，可以将存储库的 Runbook 文件夹路径中的所有 Runbook 提取到自动化帐户。 同一个存储库可以同步到多个自动化帐户。 以下是同步 Runbook 的步骤：

1. 从设置源代码管理的自动化帐户中，打开“源代码管理集成/存储库同步”页面，然后单击“同步”。在出现确认消息提示时，单击“是”以继续操作。  

    ![“同步”按钮，以及确认将要同步所有 Runbook 的消息](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. 同步功能将启动 **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** Runbook，此 Runbook 将连接到 GitHub 并将存储库中的更改拉取到 Azure 自动化。 此操作的“存储库同步”页中应该会显示一个新作业。 若要查看同步作业的详细信息，请单击以打开作业详细信息页面。  

    ![显示了针对 GitHub 存储库执行同步作业后的同步结果的窗口](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > 从源代码管理进行的同步针对当前在源代码管理中的 **所有** Runbook，覆盖当前存在于自动化帐户中的 Runbook 草稿版本。 用于同步的 Git 等效命令行指令为 **git pull**

![显示了已挂起源代码管理同步作业中的所有日志的窗口](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>断开与源代码管理的连接

若要断开与 GitHub 帐户的连接，请打开“存储库同步”页面，并单击“断开连接”。 断开与源代码管理的连接后，前面同步的 Runbook 仍会保留在自动化帐户中，但不会启用“存储库同步”页面。  

  ![显示了“断开连接”按钮的窗口，该按钮用于断开与源代码管理的连接](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>后续步骤

* 若要在 Azure 自动化中集成源代码管理，请参阅 [Azure 自动化：Azure 自动化中的源代码管理集成](https://azure.microsoft.com/blog/azure-automation-source-control-13/)。  
* 若要使用 Visual Studio Online 集成 Runbook 源代码管理，请参阅 [Azure 自动化：使用 Visual Studio Online 集成 Runbook 源代码管理](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)。  
