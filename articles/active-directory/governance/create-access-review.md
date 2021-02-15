---
title: 创建对组和应用程序的访问评审 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中创建对组成员或应用程序访问的访问评审。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778517"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中创建对组和应用程序的访问评审

员工和来宾对组和应用程序的访问权限会不断变化。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 针对组成员或应用程序访问权限创建访问评审。 如果需要定期评审访问权限，则还可以创建定期访问评审。 有关这些方案的详细信息，请参阅[管理用户访问权限](manage-user-access-with-access-reviews.md)和[管理来宾访问权限](manage-guest-access-with-access-reviews.md)。

你可以观看有关如何启用访问评审的快速视频：

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

本文介绍如何针对组成员或应用程序访问权限创建一个或多个访问评审。

## <a name="prerequisites"></a>必备条件

- Azure AD Premium P2
- 全局管理员或用户管理员

有关详细信息，请参阅[许可证要求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>创建一个或多个访问评审

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

2. 在左侧菜单中，单击“访问评审”。 

3. 单击“新建访问评审”创建新的访问评审。 

    ![标识治理中的“访问评审”窗格](./media/create-access-review/access-reviews.png)

4. 在 " **步骤1：选择要查看的内容** " 中，选择想要查看的资源。

    ![创建访问评审 - 评审名称和说明](./media/create-access-review/select-what-review.png)

5. 如果在步骤1中选择了 " **团队 + 组** "，则步骤2中有两个选项
   - **具有来宾用户的所有 Microsoft 365 组。** 如果你想要在组织中的所有 Microsoft 团队和 M365 组之间创建定期审阅，请选择此选项。 你可以选择通过单击 "选择组 (s) 排除" 来排除某些组。
   - **选择团队 + 组。** 如果要指定要查看的一组有限团队和/或组，请选择此选项。 单击此选项后，将看到要从中进行选择的组的列表。

     ![团队和组](./media/create-access-review/teams-groups.png)

     ![在用户界面中选择的团队和组](./media/create-access-review/teams-groups-detailed.png)

6. 如果在步骤1中选择了 " **应用程序** "，则可以选择步骤2中的一个或多个应用程序。

    >[!NOTE]
    > 选择多个组和/或应用程序将导致创建多个访问评审。 例如，如果你选择5个要查看的组，这将导致5个单独的访问评审

   ![选择应用程序而不是组时显示的接口](./media/create-access-review/select-application-detailed.png)

7. 接下来，在步骤3中，你可以选择要查看的范围。 你的选项是
   - **仅限来宾用户。** 选择此选项可将访问评审限制为目录中 Azure AD B2B 来宾用户。
   - **人人.** 选择此选项可将访问评审的范围限定为与资源关联的所有用户对象。

    >[!NOTE]
    > 如果在步骤2中选择了 "所有具有来宾用户的 Microsoft 365 组，则唯一的选择是在步骤3中查看来宾用户

8. 单击 "下一步"：审核
9. 在 " **选择审阅者** " 部分中，选择一个或多个人员执行访问评审。 可以选择：
    - **组所有者 (s)** 仅在对团队或组执行评审时可用 () 
    - **选定的用户 (s) 或组 ()**
    - **用户评审自己的访问权限**
    - **(预览) 用户管理者。**
    如果你选择 "用户" 或 "**组所有者**"**的管理者**，你还可以选择指定回退审阅者。 当用户未在目录中指定任何经理或组不具有所有者时，请求回退审阅者进行评审。

    ![新的访问评审](./media/create-access-review/new-access-review.png)

10. 在 " **指定重复执行的检查** " 部分中，可以指定频率 **，如每周、每月、每季度、半年**、每年。 然后指定一个 **持续时间**，用于定义将为审阅者的输入打开审阅的时间长度。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。 你可能希望缩短持续时间，以确保之前已应用你的审阅者输入。 接下来，可以选择 " **开始日期**" 和 " **结束日期**"。

    ![选择评审的发生频率](./media/create-access-review/frequency.png)

11. 单击页面底部的 " **下一步：设置** " 按钮
12. 在 " **完成** 后" 设置中，可以指定在评审完成后发生的情况

    ![创建访问评审-完成后设置](./media/create-access-review/upon-completion-settings-new.png)

如果要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用” 。 若要在评审完成后手动应用结果，请将开关设置为“禁用”。 
使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。 此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

- **不更改** - 将用户访问权限保持不变
- **删除访问权限** - 删除用户的访问权限
- **批准访问权限** - 批准用户的访问权限
- **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

    ![完成后设置选项](./media/create-access-review/upon-completion-settings-new.png)

使用在拒绝的 **来宾** 用户上应用的操作，以指定在来宾用户被拒绝时所发生的情况。
- 删除用户的资源成员身份会删除被拒绝的用户对要查看的组或应用程序的访问权限，他们仍可以登录到租户。
- 阻止用户登录30天，然后从租户中删除用户将阻止拒绝的用户登录到租户，无论他们是否有权访问其他资源。 如果出现错误，或者管理员决定重新启用某人的访问权限，该管理员可以在用户被禁用后 30 天内执行此操作。 如果没有对禁用的用户执行任何操作，则会从租户中删除这些用户。

若要详细了解删除不能再访问你组织中的资源的来宾用户的最佳做法，请阅读标题为 [使用 Azure AD Identity Governance 的文章，查看并删除不再具有资源访问权限的外部用户。](access-reviews-external-users.md)

   >[!NOTE]
   >对于超出来宾用户的审核，无法对被拒绝的来宾用户进行的操作配置。 对于 **包含来宾用户的所有 M365 组**，它也不可配置。 无法配置时，会在拒绝的用户上使用从资源中删除用户的成员身份的默认选项。

13. 在 " **启用评审决策帮助** 程序" 中，选择你是否希望你的审阅者在评审过程中接收建议。

    ![启用决策助手选项](./media/create-access-review/helpers.png)

14. 在 " **高级设置** " 部分中，可以选择以下项
    - 设置 "**启用**"**所需的理由**，要求审阅者提供审批原因。
    - 设置 **电子邮件通知** **，让 Azure AD** 在访问评审开始时以及在审阅完成时向管理员发送电子邮件通知。
    - 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。   在评审过程中，这些提醒将为自半方。
    - 发送给审阅者的电子邮件的内容根据查看详细信息（如审阅名称、资源名称、截止日期等）自动生成。如果你需要一种方式来传达其他信息（如其他说明或联系人信息），则可以在 " **审阅者电子邮件的其他内容** " 部分中指定这些详细信息。 你输入的信息将包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下图中突出显示的部分显示此信息显示的位置。


      ![审阅者的其他内容](./media/create-access-review/additional-content-reviewer.png)

15. 单击 " **下一步"：查看 "+ 创建** " 转到下一页
16. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。
17. 查看信息并选择 "**创建**"

       ![创建查看屏幕](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。 访问评审将显示在列表中，并带有其状态指示器。

![访问评审及其状态的列表](./media/create-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审对组或应用程序的访问权限](perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己对组或应用程序的访问权限](review-your-access.md)的说明。

如果将来宾指定为评审者，但他们尚未接受邀请，则他们不会收到来自访问评审的电子邮件，因为在评审前必须先接受邀请。

## <a name="access-review-status-table"></a>访问评审状态表

| 状态 | 定义 |
|--------|------------|
|NotStarted | 已创建评审，用户发现正在等待启动。 |
|正在初始化   | 正在进行用户发现，以标识属于评审的所有用户。 |
|正在启动 | 正在启动评审。 如果启用电子邮件通知，则会将电子邮件发送给评审者。 |
|正在进行 | 已开始评审。 如果启用电子邮件通知，则电子邮件已发送给评审者。 评审者可以在截止日期之前提交决定。 |
|正在完成 | 评审正在完成，电子邮件将发送到评审所有者。 |
|正在自动评审 | 评审处于系统评审阶段。 系统正在为未根据建议或预先配置的决定评审的用户记录决定。 |
|已自动评审 | 系统已为未评审的所有用户记录了决定。 如果启用了“自动应用”，则评审可以进入“正在应用”阶段了。 |
|正在应用 | 对于已批准的用户，访问权限不会更改。 |
|已应用 | 已经从源或目录删除拒绝的用户（如果有）。 |
|失败 | 无法进行评审。 此错误可能与删除租户、进行许可证更改或进行其他内部租户更改相关。 |

## <a name="create-reviews-via-apis"></a>通过 API 创建评审

也可以使用 API 创建访问评审。 在 Azure 门户中管理组和应用程序用户的访问评审的方法也可以使用 Microsoft Graph API 来实现。 有关详细信息，请参阅 [Azure AD 访问评审 API 参考](/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 有关代码示例，请参阅[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>后续步骤

- [评审组或应用程序的访问权限](perform-access-review.md)
- [评审自己对组或应用程序的访问权限](review-your-access.md)
- [完成组或应用程序的访问评审](complete-access-review.md)