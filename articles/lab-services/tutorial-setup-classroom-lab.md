---
title: 使用 Azure 实验室服务设置课堂实验室 | Microsoft Docs
description: 在本教程中，我们使用 Azure 实验室服务设置带虚拟机的课堂实验室，供学生在课堂上使用。
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787412"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教程：设置教室实验室 
在本教程中，请设置带虚拟机的课堂实验室，供学生在课堂上使用。  

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建课堂实验室
> * 将用户添加到实验室
> * 为实验室设置计划
> * 向学生发送邀请电子邮件

## <a name="prerequisites"></a>先决条件
在本教程中，我们使用课堂用虚拟机设置一个实验室。 只有充当下述角色之一的成员才能在实验室帐户中设置课堂实验室：所有者、实验室创建者或参与者。 用来创建实验室帐户的帐户会自动添加到所有者角色。 因此，我们可以使用创建实验室帐户所用的用户帐户创建一个课堂实验室。 

下面是使用 Azure 实验室服务时的典型工作流：

1. 实验室帐户创建者将其他用户添加到“实验室创建者”角色。 例如，实验室帐户创建者/管理员将教师添加到“实验室创建者”角色，这样教师就可以创建课堂实验室。 
2. 然后，教师创建包含 VM 的课堂实验室，并向课堂上的学生发送注册链接。 
3. 学生使用从教师处收到的注册链接来注册实验室。 注册以后，他们可以使用实验室中的 VM 完成课堂作业和家庭作业。 

## <a name="create-a-classroom-lab"></a>创建课堂实验室
此步骤在 Azure 中创建一个课堂实验室。 

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 请注意，尚不支持 Internet Explorer 11。 
2. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 选择“新建实验室”。 
    
    ![此屏幕截图显示了选中“新建实验室”按钮的“Azure 实验室服务”。](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的 **名称**，然后选择“下一步”。  

        ![创建课堂实验室](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. 在“虚拟机凭据”页上，指定实验室中所有 VM 的默认凭据。 指定用户的 **名称** 和 **密码**，然后选择“下一步”。  

        ![“新建实验室”窗口](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 在“实验室策略”页上，选择“完成”。  

        ![每个用户的配额](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 此时会看到以下屏幕，显示模板 VM 的创建状态。 此操作最多需要 20 分钟。 

    ![模板 VM 的创建状态](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在“模板”页上执行以下步骤：对于此教程来说，这些步骤是 **可选** 的。

    1. 选择“连接”以连接到模板 VM。 如果它是 Linux 模板 VM，请选择是要使用 SSH 还是 RDP（如果已启用 RDP）来建立连接。
    3. 在模板 VM 上安装并配置课堂所需软件。 
    4. **停止** 模板 VM。  

    > [!NOTE]
    > 模板 VM 在运行时会产生成本，因此，请确保在不需要运行模板 VM 时将它关闭。 

## <a name="publish-the-template-vm"></a>发布模板 VM
在此步骤中，我们发布模板 VM。 你发布模板 VM 时，Azure 实验室服务会使用该模板在实验室中创建 VM。 所有虚拟机的配置都与模板相同。

1. 在“模板”页上的工具栏中选择“发布”。 

    ![“发布模板”按钮](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
2. 在“发布模板”页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。 

    ![发布模板 - VM 数](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 可以在页面上查看模板的 **发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 等待发布完成，然后选择左侧菜单上的“虚拟机”或选择“虚拟机”磁贴，切换到“虚拟机池”页。   确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > 当教师打开学生 VM 时，该学生的配额不会受到影响。 用户配额指定用户在计划上课时间之外可用的实验室小时数。 有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="set-a-schedule-for-the-lab"></a>为实验室设置计划
为实验室创建计划事件，这样实验室中的 VM 就会在特定时间自动启动/停止。 此前指定的用户配额（默认值：10 小时）是在此计划时间之外为每个用户分配的额外时间。 

1. 切换到“日程安排”页，再选择工具栏上的“添加计划事件”。 

    ![此屏幕截图显示了“日程安排”页上的“添加计划事件”按钮。](./media/how-to-create-schedules/add-schedule-button.png)
2. 在“添加计划事件”页上执行以下步骤：
    1. 确认已选择“标准”作为“事件类型”。   
    2. 选择课堂的“开始日期”。 
    4. 选择“启动时间”，这是你希望 VM 启动的时间。
    5. 选择“停止时间”，这是 VM 将要关闭的时间。 
    6. 选择“时区”，该时区适用于已指定的启动时间和停止时间。 
3. 在同一“添加计划事件”页上的“重复”部分，选择当前计划。   

    ![“日程安排”页上的“添加日程安排”按钮](./media/how-to-create-schedules/select-current-schedule.png)
5. 在“重复”对话框中，执行以下步骤：
    1. 对于“重复”字段，请确认已设置“每周” 。 
    2. 选择希望计划生效的日期。 在下面的示例中，选中的是“星期一”至“星期五”。 
    3. 选择计划的 **结束日期**。
    8. 选择“保存”。 

        ![设置重复计划](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 现在，请在“添加计划事件”页上输入该计划的任何说明或备注作为“备注(可选)”。 
4. 在“添加计划事件”页上，选择“保存”。 

    ![每周日程安排](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. 在日历中导航到开始日期，确认是否已设置该计划。
    
    ![日历中的计划](./media/how-to-create-schedules/schedule-calendar.png)

    有关创建和管理课堂计划的详细信息，请参阅[创建和管理实验室的计划](how-to-create-schedules.md)。


## <a name="add-users-to-the-lab"></a>将用户添加到实验室

添加用户时，默认情况下，“限制访问”选项处于打开状态，除非他们在用户列表中，否则即使学生有注册链接，他们也无法注册到实验室。 只有列出的用户可以使用你发送的注册链接注册到实验室。 你可以关闭“限制访问”，这样一来，学生只要有注册链接就可以注册到实验室。 

### <a name="add-users-from-an-azure-ad-group"></a>从 Azure AD 组添加用户

可以将实验室用户列表同步到现有 Azure Active Directory (Azure AD) 组，这样就无需手动添加或删除用户。 

可以在组织的 Azure Active Directory 中创建 Azure AD 组，以管理对组织资源和基于云的应用的访问。 若要了解详细信息，请参阅 [Azure AD 组](../active-directory/fundamentals/active-directory-manage-groups.md)。 如果组织使用 Microsoft Office 365 或 Azure 服务，则组织已有管理 Azure Active Directory 的管理员。 

> [!IMPORTANT]
> 确保用户列表为空。 如果实验室中存在手动添加或通过导入 CSV 文件添加的现有用户，则不会显示用于将实验室同步到现有组的选项。 

1. 在左窗格中，选择“用户”。 
1. 单击“从组同步”。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="通过从 Azure AD 组同步来添加用户":::
    
1. 系统将提示你选择要将实验室同步到的现有 Azure AD 组。 
    
    如果在列表中看不到 Azure AD 组，可能是由于以下原因造成的：

    -   如果你是 Azure Active Directory 的来宾用户（通常是在拥有 Azure AD 的组织之外），你将无法在 Azure AD 内搜索组。 在这种情况下，你将无法向实验室添加 Azure AD 组。 
    -   通过 Teams 创建 Azure AD 组不会显示在此列表中。 你可以在 Teams 内添加 Azure 实验室服务应用，以便直接从中创建和管理实验室。 请参阅有关[从 Teams 内部管理实验室用户列表](how-to-manage-user-lists-within-teams.md)的详细信息。 
1. 选择要将实验室同步到 Azure AD 组后，单击“添加”。
1. 同步实验室后，它会将 Azure AD 组内的所有人作为用户拉入实验室，你将看到更新的用户列表。 只有此 Azure AD 组中的人员才能访问你的实验室。 用户列表将每 24 小时刷新一次，以与 Azure AD 组的最新成员身份匹配。 还可以单击“用户”选项卡中的“同步”按钮，手动同步到 Azure AD 组中的最新更改。
1. 单击“全部邀请”按钮邀请用户加入实验室，这将向所有用户发送一封电子邮件，其中包含实验室的注册链接。 

### <a name="add-users-manually-from-emails-or-csv-file"></a>从电子邮件或 CSV 文件中手动添加用户

在本部分中，你将手动添加学生（通过电子邮件地址或上传 CSV 文件）。 

#### <a name="add-users-by-email-address"></a>按电子邮件地址添加用户

1. 在左窗格中，选择“用户”。 
1. 单击“手动添加用户”。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="手动添加用户":::
1. 选择“按电子邮件地址添加”（默认），在单独的行或用分号分隔的单个行上输入学生的电子邮件地址。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="添加用户的电子邮件地址":::
1. 选择“保存”。 

    该列表显示当前用户的电子邮件地址和状态，无论他们是否已注册到实验室。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="用户列表":::

    > [!NOTE]
    > 学生注册到实验室后，列表将显示其名称。 列表中显示的名称是按 Azure Active Directory 中学生的名字和姓氏构造的。 

#### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户

还可以通过上载包含其电子邮件地址的 CSV 文件来添加用户。 

CSV 文本文件用于存储以逗号分隔 (CSV) 的表格数据（数字和文本）。 CSV 文件存储以逗号分隔的信息，而不是将信息存储在列字段中（如电子表格中）。 CSV 文件中的每一行都相同数量的以逗号分隔的“字段”。 可以使用 Excel 轻松创建和编辑 CSV 文件。

1. 在 Microsoft Excel 中，创建一个在一列中列出学生电子邮件地址的 CSV 文件。

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV 文件中的用户列表":::
1. 在“用户”窗格的顶部，选择“添加用户”，然后选择“上传 CSV”  。
1. 选择包含学生电子邮件地址的 CSV 文件，然后选择“打开”。

    “添加用户”窗口显示 CSV 文件中的电子邮件地址列表。 
1. 选择“保存”。 
1. 在“用户”窗格中，查看添加的学生列表。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="用户”窗格中已添加用户的列表"::: 

## <a name="send-invitation-emails-to-users"></a>向用户发送邀请电子邮件

1. 切换到“用户”视图（如果尚未转到该页），然后在工具栏上选择“全部邀请”。  

    ![选择学生](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. 在“通过电子邮件发送邀请”页上，输入可选的消息，然后选择“发送”。  电子邮件自动包含注册链接。 若要获取此注册链接，可以选择工具栏上的“... (省略号)”，然后选择“注册链接”。  

    ![通过电子邮件发送注册链接](./media/tutorial-setup-classroom-lab/send-email.png)
4. 可以在“用户”列表中查看 **邀请** 状态。 状态应更改为“正在发送”，然后更改为“已在 &lt;日期&gt; 发送”。  

若要详细了解如何将学生添加到课堂并管理其对实验室的使用，请参阅[如何配置学生对实验室的使用](how-to-configure-student-usage.md)。

## <a name="next-steps"></a>后续步骤
在本教程中，你在 Azure 中创建了一个课堂实验室。 若要了解学生如何使用注册链接访问实验室中的 VM，请转到下一教程：

> [!div class="nextstepaction"]
> [连接到课堂实验室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)