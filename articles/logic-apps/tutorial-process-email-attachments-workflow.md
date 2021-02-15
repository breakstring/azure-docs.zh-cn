---
title: 使用多个 Azure 服务自动执行任务
description: 教程 - 创建自动化工作流，以便使用 Azure 逻辑应用、Azure 存储和 Azure Functions 处理电子邮件
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: bd1715dc0a3767bc5826154616bbdc97c7b61dd3
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576357"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>教程：使用 Azure 逻辑应用、Azure Functions 和 Azure 存储来自动执行处理电子邮件的任务

Azure 逻辑应用有助于跨 Azure 服务、Microsoft 服务、其他软件即服务 (SaaS) 应用以及本地系统自动完成工作流和集成数据。 本教程介绍如何生成可以处理传入电子邮件和任何附件的[逻辑应用](../logic-apps/logic-apps-overview.md)。 此逻辑应用分析电子邮件内容，将内容保存到 Azure 存储，然后发送查看该内容的通知。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 [Azure 存储](../storage/common/storage-introduction.md)和存储资源管理器，以便查看保存的电子邮件和附件。
> * 创建用于从电子邮件中删除 HTML 的 [Azure 函数](../azure-functions/functions-overview.md)。 本教程包括可以用于此函数的代码。
> * 创建空白逻辑应用。
> * 添加一个触发器，用于监视电子邮件中的附件。
> * 添加一个条件，用于检查电子邮件是否有附件。
> * 添加一个操作，用于在电子邮件有附件时调用 Azure 函数。
> * 添加一个操作，用于为电子邮件和附件创建存储 Blob。
> * 添加一个操作，用于发送电子邮件通知。

完成后，逻辑应用看起来大致与以下工作流类似：

![完成的逻辑应用概览](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 逻辑应用支持的电子邮件提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](/connectors/)。

  此逻辑应用使用工作或学校帐户。 如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 显示可能稍有不同。

  > [!IMPORTANT]
  > 如果要使用 Gmail 连接器，则只有 G-Suite 商业帐户可以在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则只能将此连接器与 Google 批准的特定服务一起使用，也可以[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* 下载并安装[免费 Microsoft Azure 存储资源管理器](https://storageexplorer.com/)。 此工具可以用于检查存储容器是否已正确设置。

* 如果逻辑应用需要通过将流量限制为特定 IP 地址的防火墙进行通信，则该防火墙需要允许访问逻辑应用所在的 Azure 区域中的逻辑应用服务或运行时所使用的[入站](logic-apps-limits-and-config.md#inbound)和[出站](logic-apps-limits-and-config.md#outbound) IP 地址。 如果逻辑应用还使用[托管连接器](../connectors/apis-list.md#managed-api-connectors)（如 Office 365 Outlook 连接器或 SQL 连接器），或使用[自定义连接器](/connectors/custom-connectors/)，则防火墙还需要允许访问逻辑应用的 Azure 区域中的所有[托管连接器出站 IP 地址](logic-apps-limits-and-config.md#outbound)。

## <a name="set-up-storage-to-save-attachments"></a>设置用于保存附件的存储。

可以在 [Azure 存储容器](../storage/common/storage-introduction.md)中以 Blob 形式保存传入电子邮件和附件。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在创建存储容器之前，请先在 Azure 门户中的“基本信息”选项卡上使用以下设置[创建一个存储帐户](../storage/common/storage-account-create.md)：

   | 设置 | 值 | 说明 |
   |---------|-------|-------------|
   | **订阅** | <*Azure-subscription-name*> | Azure 订阅的名称 |  
   | **资源组** | <Azure-resource-group> | 用于组织和管理相关资源的 [Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 此示例使用“LA-Tutorial-RG”。 <p>**注意：** 资源组存在于特定的区域内。 本教程中的项目可能不在所有区域提供，请尽可能尝试使用同一区域。 |
   | **存储帐户名称** | <*Azure-storage-account-name*> | 你的存储帐户名称，必须包含 3-24 个字符，并且只能包含小写字母和数字。 此示例使用“attachmentstorageacct”。 |
   | **位置** | <*Azure-region*> | 用于存储存储帐户相关信息的区域。 此示例使用“美国西部”。 |
   | **“性能”** | 标准 | 此设置指定支持的数据类型以及用于存储数据的介质。 请参阅[存储帐户的类型](../storage/common/storage-introduction.md#types-of-storage-accounts)。 |
   | 帐户类型 | 常规用途 | [存储帐户类型](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **复制** | 本地冗余存储 (LRS) | 此设置指定如何复制、存储、管理和同步数据。 请参阅[本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](../storage/common/storage-redundancy.md)。 |
   | **访问层（默认）** | 保留当前设置。 |
   ||||

   在“高级”选项卡上选择以下设置：

   | 设置 | 值 | 说明 |
   |---------|-------|-------------|
   | 需要安全传输 | 已禁用 | 此设置指定从连接进行请求所需的安全性。 请参阅[需要安全传输](../storage/common/storage-require-secure-transfer.md)。 |
   ||||

   若要创建存储帐户，也可以使用 [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) 或 [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli)。

1. 完成操作后，选择“查看 + 创建”。

1. 在 Azure 部署存储帐户后，请查找存储帐户并获取存储帐户的访问密钥：

   1. 在存储帐户菜单中的“设置”下，选择“访问密钥”。

   1. 复制存储帐户名称和“密钥 1”，然后将这些值保存到某个安全位置。

      ![复制并保存存储帐户名称和密钥](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   若要获取存储帐户的访问密钥，也可以使用 [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) 或 [Azure CLI](/cli/azure/storage/account/keys)。

1. 为电子邮件附件创建 Blob 存储容器。

   1. 在存储帐户菜单中选择“概述”。 在“概述”窗格上，选择“容器”。

      ![添加 Blob 存储容器](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. “容器”页打开后，在工具栏上选择“容器”。

   1. 在“新建容器”下，输入 `attachments` 作为容器名称。 在“公共访问级别”下依次选择“容器(对容器和 Blob 进行匿名读取访问)” > “确定”  。

      完成后，即可在 Azure 门户的存储帐户中找到存储容器，如下所示：

      ![完成的存储容器](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   若要创建存储容器，也可以使用 [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) 或 [Azure CLI](/cli/azure/storage/container#az-storage-container-create)。

接下来，将存储资源管理器连接到存储帐户。

## <a name="set-up-storage-explorer"></a>设置存储资源管理器

现在，请将存储资源管理器连接到存储帐户，以确认逻辑应用能否正确地在存储容器中将附件保存为 Blob。

1. 启动 Microsoft Azure 存储资源管理器

   存储资源管理器将提示你提供存储帐户的连接。

1. 在“连接到 Azure 存储”窗格中，依次选择“使用存储帐户名称和密钥” > “下一步”  。

   ![存储资源管理器 - 连接到存储帐户](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > 如果未显示提示，请在存储资源管理器工具栏上选择“添加帐户”。

1. 在“显示名称”下为连接提供一个易记名称。 在“帐户名称”下，提供存储帐户名称。 在“帐户密钥”下，提供前面保存的访问密钥然后选择“下一步” 。

1. 确认连接信息，然后选择“连接”。

   存储资源管理器会创建连接，并在“资源管理器”窗口中的“本地和附加” > “存储帐户”下显示你的存储帐户 。

1. 若要查找自己的 Blob 存储容器，请在“存储帐户”下展开自己的存储帐户（此处为 attachmentstorageacct），然后展开“Blob 容器”，在其中可以找到“附件”容器，例如   ：

   ![存储资源管理器 - 查找存储容器](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

接下来，创建用于从传入电子邮件中删除 HTML 的 [Azure 函数](../azure-functions/functions-overview.md)。

## <a name="create-function-to-clean-html"></a>创建用于清理 HTML 的函数

现在，请使用这些步骤提供的代码片段，创建一个可从每封传入电子邮件中删除 HTML 的 Azure 函数。 这样就可以让电子邮件内容更干净，更容易处理。 然后即可从逻辑应用调用此函数。

1. 请先使用以下设置[创建函数应用](../azure-functions/functions-create-function-app-portal.md)，然后即可创建函数：

   | 设置 | 值 | 说明 |
   | ------- | ----- | ----------- |
   | **应用名称** | <*function-app-name*> | 函数应用的名称，该名称在 Azure 中必须是全局唯一的。 此示例已在使用“CleanTextFunctionApp”，所以请提供不同的名称，如“MyCleanTextFunctionApp-<your-name>” |
   | **订阅** | <*your-Azure-subscription-name*> | 以前使用过的同一 Azure 订阅 |
   | **资源组** | LA-Tutorial-RG | 以前使用过的同一 Azure 资源组 |
   | **OS** | <your-operating-system> | 选择支持你偏好的函数编程语言的操作系统。 对于此示例，请选择“Windows”。 |
   | **托管计划** | 消耗计划 | 此设置决定了如何分配和缩放用于运行函数应用的资源，例如计算能力。 请参阅[托管计划比较](../azure-functions/functions-scale.md)。 |
   | **位置** | 美国西部 | 以前使用过的同一区域 |
   | **运行时堆栈** | 首选语言 | 选择支持你偏好的函数编程语言的运行时。 对于 C# 和 F# 函数，选择“.NET”。 |
   | **存储** | cleantextfunctionstorageacct | 为函数应用创建存储帐户。 只使用小写字母和数字。 <p>**注意：** 此存储帐户包含函数应用，不同于以前创建的用于电子邮件附件的存储帐户。 |
   | **Application Insights** | 禁用 | 请对 [Application Insights](../azure-monitor/app/app-insights-overview.md) 启用应用程序监视，但对于本教程，请选择“禁用” > “应用” 。 |
   ||||

   如果函数应用没有在部署后自动打开，请在 [Azure 门户](https://portal.azure.com)搜索框中找到该应用并选择“函数应用”。 在“函数应用”下选择你的函数应用。

   ![选择函数应用](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   否则，Azure 会自动打开你的函数应用，如下所示：

   ![创建的函数应用](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   若要创建函数应用，也可以使用 [Azure CLI](../azure-functions/create-first-function-cli-csharp.md) 或 [PowerShell 和资源管理器模板](../azure-resource-manager/templates/deploy-powershell.md)。

1. 在“函数应用”列表中展开函数（如果尚未展开）。 在你的函数应用下，选择“函数”。 在函数工具栏上选择“新建函数”。

   ![创建新的函数](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. 在“选择下面的模板或转到快速入门”下，选择“HTTP 触发器”函数模板。

   ![选择“HTTP 触发器”模板](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure 将使用 HTTP 触发的函数的语言特定模板创建一个函数。

1. 在“新建函数”窗格中的“名称”下，输入 `RemoveHTMLFunction`。 将“授权级别”设置保留为“函数”，然后选择“创建”  。

   ![为函数命名](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. 打开编辑器后，将模板代码替换为以下示例代码，以便删除 HTML 并将结果返回给调用方：

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. 完成后，选择“保存”。 若要测试函数，请在编辑器右边缘的箭头 (<) 图标下，选择“测试” 。

   ![打开“测试”窗格。](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. 在“测试”窗格中的“请求正文”下，输入以下代码行，然后选择“运行”  。

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![测试函数](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   “输出”窗口显示函数的结果：

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

在检查函数正常以后，创建逻辑应用。 虽然本教程介绍的是如何创建从电子邮件中删除 HTML 的函数，但逻辑应用还提供了“HTML 到文本”连接器。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 在 Azure 顶层搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

   ![查找并选择“逻辑应用”](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. 在“逻辑应用”窗格中，选择“添加”。

   ![添加新逻辑应用](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. 在“逻辑应用”窗格上，提供有关逻辑应用的详细信息，如下所示。 完成后，选择“查看 + 创建”。

   ![提供逻辑应用信息](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | 设置 | 值 | 说明 |
   | ------- | ----- | ----------- |
   | **订阅** | <*your-Azure-subscription-name*> | 以前使用过的同一 Azure 订阅 |
   | **资源组** | LA-Tutorial-RG | 以前使用过的同一 Azure 资源组 |
   | **逻辑应用名称** | LA-ProcessAttachment | 逻辑应用的名称 |
   | **选择位置** | 美国西部 | 以前使用过的同一区域 |
   | **Log Analytics** | 关闭 | 对于本教程，请选择“关闭”设置。 |
   ||||

1. 在 Azure 部署你的应用后，在 Azure 工具栏上选择通知图标然后选择“转到资源”。

   ![在 Azure 通知列表中，选择“转到资源”](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. 逻辑应用设计器此时将打开并显示一个页面，其中包含简介视频以及针对常用逻辑应用模式的模板。 在“模板”下选择“空白逻辑应用”。

   ![选择空白逻辑应用模板](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

接下来，请添加一个[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)，侦听有附件的传入电子邮件。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="monitor-incoming-email"></a>监视传入电子邮件

1. 在设计器上的搜索框中，输入 `when new email arrives` 作为筛选器。 针对电子邮件提供程序选择以下触发器：**新电子邮件到达时 - <*your-email-provider*>**

   例如：

   ![针对电子邮件提供程序选择以下触发器：“新电子邮件到达时”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。

   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。

1. 如果系统要求提供凭据，请登录到自己的电子邮件帐户，以便逻辑应用可以连接到该电子邮件帐户。

1. 现在，请提供触发器用来筛选新电子邮件的条件。

   1. 指定下面介绍的用于检查电子邮件的设置。

      ![指定用于检查邮件的文件夹、时间间隔和频率](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | 设置 | 值 | 说明 |
      | ------- | ----- | ----------- |
      | **文件夹** | Inbox | 要检查的电子邮件文件夹 |
      | **带有附件** | 是 | 仅获取带有附件的电子邮件。 <p>**注意：** 此触发器不删除帐户中的任何电子邮件，仅检查新邮件，并且仅处理与主题筛选器匹配的电子邮件。 |
      | **包括附件** | 是 | 获取充当工作流输入的附件，而不是仅仅检查是否有附件。 |
      | **时间间隔** | 1 | 在两次检查之间需等待的时间间隔数 |
      | **频率** | Minute | 两次检查的间隔的时间单位 |
      ||||

   1. 在“添加新参数”列表中，选择“主题筛选器”。

   1. 操作中显示“主题筛选器”框之后，请按以下列表中所示指定主题：

      | 设置 | 值 | 说明 |
      | ------- | ----- | ----------- |
      | **主题筛选器** | `Business Analyst 2 #423501` | 要在电子邮件主题中查找的文本 |
      ||||

1. 若要立即隐藏触发器的详细信息，请单击触发器的标题栏。

   ![折叠形状即可隐藏详细信息](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。

   逻辑应用现已生成，但除了检查电子邮件，不能执行任何操作。 接下来，请添加一个条件，用于指定继续执行工作流所需的条件。

## <a name="check-for-attachments"></a>检查是否有附件

现在添加一个条件，以便仅选择包含附件的电子邮件。

1. 在触发器下，选择“新建步骤”。

   ![“新建步骤”](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. 在“选择操作”下的搜索框中输入 `condition`。 选择以下操作：**条件**

   ![选择“条件”](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. 重命名此条件，并提供更好的说明。 在条件的标题栏中，依次选择省略号 (…) 按钮 >“重命名” 。

      ![重命名条件](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. 重命名条件并提供以下说明：`If email has attachments and key subject phrase`

1. 创建一个条件，用于检查包含附件的电子邮件。

   1. 在“和”下面的第一行中，单击左侧框的内部。 从显示的动态内容列表中，选择“包含附件”属性。

      ![显示选择了条件的“And”属性和“Has Attachment”属性的屏幕截图。](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. 在中间框中，保留运算符“等于”。

   1. 在右侧框中输入 **true**，该值将与触发器中的“包含附件”属性值进行比较。

      ![构建条件](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      如果两个值相等，则电子邮件至少有一个附件，条件通过，工作流继续。

   在可在代码编辑器窗口中查看的基础逻辑应用定义中，此条件如以下示例所示：

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。

### <a name="test-your-condition"></a>测试条件

现在，请测试条件是否正常工作：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

   此步骤手动启动逻辑应用，不需等待指定的时间间隔。 但在测试性电子邮件到达收件箱之前，什么也不会发生。

1. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的 **主题筛选器** 中指定的：`Business Analyst 2 #423501`

   * 电子邮件有一个附件。 现在，请直接创建一个空的文本文件，然后将该文件附加到电子邮件。

   当电子邮件到达时，逻辑应用会检查其中是否有附件和指定的主题文本。 如果满足条件，触发器就会触发，促使逻辑应用引擎创建一个逻辑应用实例并启动工作流。

1. 若要检查触发器是否已成功触发以及逻辑应用是否已成功运行，请在逻辑应用菜单中选择“概览”。

   ![检查触发器和运行历史记录](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

接下来，定义需要针对 **If true** 分支执行的操作。 若要保存电子邮件及其附件，请从电子邮件正文中删除 HTML，然后在电子邮件和附件的存储容器中创建 Blob。

> [!NOTE]
> 当电子邮件没有附件时，逻辑应用不需针对 **If false** 分支执行任何操作。
> 完成本教程后，可以针对 **If false** 分支添加需要执行的适当操作，作为附加练习。

## <a name="call-removehtmlfunction"></a>调用 RemoveHTMLFunction

此步骤将前面创建的 Azure 函数添加到逻辑应用，并将电子邮件正文内容从电子邮件触发器传递给函数。

1. 在逻辑应用菜单中选择“逻辑应用设计器”。 在“If true”分支中，选择“添加操作” 。

   ![在“If true”中添加操作](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. 在搜索框中，查找“Azure functions”，然后选择以下操作：**选择 Azure 函数 - Azure Functions**

   ![为“选择 Azure 函数”选择操作](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. 选择之前创建的函数应用，在本例中为 `CleanTextFunctionApp`：

   ![选择 Azure 函数应用](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. 现在，选择你的函数：**RemoveHTMLFunction**

   ![选择 Azure 函数](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. 重命名函数形状并提供以下说明：`Call RemoveHTMLFunction to clean email body`

1. 现在，请指定函数要处理的输入。

   1. 在“请求正文”下，输入以下带尾随空格的文本：

      `{ "emailBody":`

      在后续步骤中处理此输入时，会一直显示有关 JSON 无效的错误，直到正确将输入格式设置为 JSON。 以前测试此函数时，为此函数指定的输入使用 JavaScript 对象表示法 (JSON)。 因此，请求正文也必须使用同一格式。

      此外，将光标置于“请求正文”框中时，会显示动态内容列表，以便可以选择前述操作中可用的属性值。

   1. 在动态内容列表中的“收到新电子邮件时”下，选择“正文”属性。 请记得在此属性的后面添加右大括号：`}`

      ![指定要传递到函数的请求正文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   完成后，函数的输入如以下例所示：

   ![现已完成要传递给函数的请求正文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. 保存逻辑应用。

接下来，请添加一个操作，以便在存储容器中创建用于保存电子邮件正文的 Blob。

## <a name="create-blob-for-email-body"></a>创建用于电子邮件正文的 Blob

1. 在“If true”块中和 Azure 函数的下面，选择“添加操作” 。

1. 在搜索框中，输入 `create blob` 作为筛选器，然后选择以下操作：**创建 Blob**

   ![添加为电子邮件正文创建 Blob 的操作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. 如下所示和所述，使用这些设置来与存储帐户建立连接。 完成操作后，选择“创建”。

   ![创建到存储帐户的连接](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | 设置 | 值 | 说明 |
   | ------- | ----- | ----------- |
   | **连接名称** | AttachmentStorageConnection | 连接的描述性名称 |
   | **存储帐户** | attachmentstorageacct | 以前创建的用于保存附件的存储帐户的名称 |
   ||||

1. 重命名“创建 Blob”操作并提供以下说明：`Create blob for email body`

1. 在“创建 Blob”操作中，按如下所示和所述提供信息，并选择创建 Blob 所需的字段：

   ![提供适用于电子邮件正文的 Blob 信息](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | 设置 | 值 | 说明 |
   | ------- | ----- | ----------- |
   | **文件夹路径** | /attachments | 以前创建的容器的路径和名称。 对于此示例，请单击文件夹图标，然后选择“/attachments”容器。 |
   | **Blob 名称** | “发件人”字段 | 对于此示例，请使用发件人的姓名作为 Blob 名称。 单击此框内部以显示动态内容列表，然后在“收到新电子邮件时”操作下面选择“发件人”字段。 |
   | **Blob 内容** | “内容”字段 | 对于此示例，请使用不包含 HTML 的电子邮件正文作为 Blob 内容。 单击此框内部以显示动态内容列表，然后在“调用 RemoveHTMLFunction 来清理电子邮件正文”操作下面选择“正文”。 |
   ||||

   完成后，此操作如以下示例所示：

   ![屏幕截图显示了已完成的“创建 blob”操作的示例。](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. 保存逻辑应用。

### <a name="check-attachment-handling"></a>检查附件处理情况

现在，请测试逻辑应用是否采用指定的方式来处理电子邮件：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

1. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题的文本是在触发器的 **主题筛选器** 中指定的：`Business Analyst 2 #423501`

   * 电子邮件至少有一个附件。 现在，请直接创建一个空的文本文件，然后将该文件附加到电子邮件。

   * 电子邮件的正文中有一些测试内容，例如：`Testing my logic app`

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

1. 检查逻辑应用是否已将电子邮件保存到正确的存储容器。

   1. 在存储资源管理器中，依次展开“本地和附加” > “存储帐户” > “attachmentstorageacct (密钥)” > “Blob 容器” > “attachments”    。

   1. 检查 **attachments** 容器中是否有电子邮件。

      此时只有电子邮件显示在容器中，因为逻辑应用尚未处理附件。

      ![检查存储资源管理器中是否有保存的电子邮件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. 完成后，删除存储资源管理器中的电子邮件。

1. （可选）若要测试 **If false** 分支（目前不执行任何操作），可以发送一封不符合条件的电子邮件。

接下来，添加一个用于处理所有电子邮件附件的循环。

## <a name="process-attachments"></a>处理附件

若要处理电子邮件中的每个附件，请将 **For each** 循环添加到逻辑应用的工作流。

1. 在“创建用于电子邮件正文的 Blob”形状下，选择“添加操作”。

   ![添加“for each”循环](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. 在“选择操作”下的搜索框中，输入 `for each` 作为筛选器，然后选择以下操作：**For each**

   ![选择“For each”](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. 重命名循环并提供以下说明：`For each email attachment`

1. 现在指定供循环处理的数据。 单击“从先前的步骤中选择一个输出”框的内部以打开动态内容列表，然后选择“附件”。

   ![选择“附件”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   “附件”字段传入一个数组，其中包含电子邮件随附的所有附件。 **For each** 循环针对每个通过数组传入的项目重复操作。

1. 保存逻辑应用。

接下来，添加可在 **attachments** 存储容器中将每个附件另存为 Blob 的操作。

## <a name="create-blob-for-each-attachment"></a>为每个附件创建 Blob

1. 在“For each email attachment”循环中选择“添加操作”，以便指定要对每个已发现附件执行的任务 。

   ![添加用于循环的操作](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. 在搜索框中，输入 `create blob` 作为筛选器，然后选择以下操作：**创建 Blob**

   ![添加用于创建 Blob 的操作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. 重命名“创建 Blob 2”操作并提供以下说明：`Create blob for each email attachment`

1. 在“为每个电子邮件附件创建 Blob”操作中，按如下所示和所述提供信息，并为要创建的每个 Blob 选择属性：

   ![提供 Blob 信息](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | 设置 | 值 | 说明 |
   | ------- | ----- | ----------- |
   | **文件夹路径** | /attachments | 以前创建的容器的路径和名称。 对于此示例，请单击文件夹图标，然后选择“/attachments”容器。 |
   | **Blob 名称** | “名称”字段 | 对于此示例，请使用附件的名称作为 Blob 名称。 单击此框内部以显示动态内容列表，然后在“收到新电子邮件时”操作下面选择“名称”字段。 |
   | **Blob 内容** | “内容”字段 | 对于此示例，请使用“内容”字段作为 Blob 内容。 单击此框内部以显示动态内容列表，然后在“收到新电子邮件时”操作下面选择“内容”字段。 |
   ||||

   完成后，此操作如以下示例所示：

   ![现已完成“创建 Blob”操作](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. 保存逻辑应用。

### <a name="check-attachment-handling"></a>检查附件处理情况

接下来，请测试逻辑应用是否采用指定的方式来处理附件：

1. 如果逻辑应用尚未运行，请在设计器工具栏中选择“运行”。

1. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题包含在触发器的“主题筛选器”属性中指定的文本：`Business Analyst 2 #423501`

   * 电子邮件至少有两个附件。 现在，请直接创建两个空的文本文件，然后将这两个文件附加到电子邮件。

   如果触发器成功触发，但逻辑应用没有触发或运行，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

1. 检查逻辑应用是否已将电子邮件和附件保存到正确的存储容器。

   1. 在存储资源管理器中，依次展开“本地和附加” > “存储帐户” > “attachmentstorageacct (密钥)” > “Blob 容器” > “attachments”    。

   1. 检查 **attachments** 容器中是否有电子邮件和附件。

      ![检查是否有已保存的电子邮件和附件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. 完成后，删除存储资源管理器中的电子邮件和附件。

接下来添加一项操作，使逻辑应用发送需查看附件的电子邮件。

## <a name="send-email-notifications"></a>发送电子邮件通知

1. 在“If true”分支的“For each email attachment”循环下，选择“添加操作”  。

   ![在“for each”循环下添加操作](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. 在搜索框中，输入 `send email` 作为筛选器，然后为电子邮件提供程序选择“发送电子邮件”操作。

   若要通过筛选操作列表来查找特定的服务，可以先选择连接器。

   ![为电子邮件提供商选择“发送电子邮件”操作](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。

   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。

1. 如果系统要求提供凭据，请登录电子邮件帐户，这样逻辑应用就可以创建到该电子邮件帐户的连接了。

1. 重命名“发送电子邮件”操作并提供以下说明：`Send email for review`

1. 提供此操作的信息，然后选择要包含在电子邮件中的字段，如下所示。 若要在编辑框中添加空行，请按 Shift + Enter。

   ![发送电子邮件通知](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   如果在动态内容列表中找不到所需的字段，请选择“收到新电子邮件时”旁边的“查看更多” 。

   | 设置 | 值 | 注释 |
   | ------- | ----- | ----- |
   | **To** | <*recipient-email-address*> | 为进行测试，可以使用自己的电子邮件地址。 |
   | **主题**  | ```ASAP - Review applicant for position:``` **主题** | 要包括的电子邮件主题。 单击此框的内部，输入示例文本，然后在动态内容列表中，选择“收到新电子邮件时”下面的“主题”字段。 |
   | **正文** | ```Please review new applicant:``` <p>```Applicant name:``` **发件人** <p>```Application file location:``` **路径** <p>```Application email content:``` **正文** | 电子邮件的正文内容。 单击此框的内部，输入示例文本，然后从动态内容列表中选择以下字段： <p>-“收到新电子邮件时”下的“发件人”字段 </br>-“创建用于电子邮件正文的 Blob”下的“路径”字段 </br>-“调用 RemoveHTMLFunction 来清除电子邮件正文”下的“正文”字段 |
   ||||

   > [!NOTE]
   > 如果选择了一个包含数组的字段，例如“内容”字段（包含附件的数组），设计器会围绕引用该字段的操作自动添加“For each”循环。
   > 这样一来，逻辑应用就可以对每个数组项执行该操作。
   > 若要删除该循环，请删除数组的字段，将引用操作移至循环外部，选择循环的标题栏中的省略号 (…)，然后选择“删除” 。

1. 保存逻辑应用。

现在请测试自己的逻辑应用，该应用类似于以下示例：

![完成的逻辑应用](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

1. 向自己发送一封满足以下条件的电子邮件：

   * 电子邮件主题包含在触发器的“主题筛选器”属性中指定的文本：`Business Analyst 2 #423501`

   * 电子邮件包含一个或多个附件。 可以重复使用上一测试留下的任何空的文本文件。 如需更逼真的方案，请附加一个简历文件。

   * 电子邮件正文包含以下文本（可以复制和粘贴）：

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. 运行逻辑应用。 如果成功，逻辑应用会发送一封电子邮件，如以下示例所示：

   ![逻辑应用发送的电子邮件通知](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现在已创建并运行逻辑应用。该应用可以跨不同的 Azure 服务自动完成任务，并且可以调用某些自定义代码。

## <a name="clean-up-resources"></a>清理资源

不再需要此示例时，删除包含你的逻辑应用和相关资源的资源组。

1. 在顶层 Azure 搜索框中，输入 `resources groups`，并选择“资源组”。

   ![查找并选择“资源组”](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. 从“资源组”列表中，选择本教程的资源组。 

   ![查找教程的资源组](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. 在“概述”窗格上，选择“删除资源组”。

   ![删除逻辑应用资源组](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. 出现确认窗格时，输入资源组名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何集成 Azure 存储和 Azure Functions 之类的 Azure 服务，以便创建处理和存储电子邮件附件的逻辑应用。 现在，请详细了解可以用来生成逻辑应用的其他连接器。

> [!div class="nextstepaction"]
> [详细了解适用于逻辑应用的连接器](../connectors/apis-list.md)