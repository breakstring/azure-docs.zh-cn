---
title: 教程：为 StarLeaf 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 StarLeaf 以及取消其预配。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357923"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>教程：为 StarLeaf 配置自动用户预配

本教程的目的是演示要在 StarLeaf 和 Azure Active Directory (Azure AD) 中执行的步骤，以将 Azure AD 自动预配和取消预配到 StarLeaf。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [StarLeaf 租户](https://starleaf.com/)。
* StarLeaf 中具有管理员权限的用户帐户。

## <a name="assign-users-to-starleaf"></a>将用户分配到 StarLeaf
Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和组需要访问 StarLeaf。 然后，可以按照 [这些说明](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配到 StarLeaf。

## <a name="important-tips-for-assigning-users-to-starleaf"></a>将用户分配到 StarLeaf 的重要提示

* 建议将单个 Azure AD 用户分配到 StarLeaf 以测试自动用户预配配置。 稍后可以分配其他用户和组。

* 将用户分配到 StarLeaf 时，必须在分配对话框中选择任何特定于应用程序的有效角色 (如有) 。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-starleaf-for-provisioning"></a>设置 StarLeaf 以进行预配

在将 StarLeaf 配置为 Azure AD 的自动用户预配之前，需要在 StarLeaf 中配置 SCIM 预配：

1. 登录到 [StarLeaf 管理控制台](https://portal.starleaf.com/#page=login)。 导航到 " **集成** " "  >  **添加集成** "。

    ![StarLeaf 管理控制台的屏幕截图，其中包含集成并添加了称为 out 的集成选项。](media/starleaf-provisioning-tutorial/image00.png)

2. 选择要 Microsoft Azure Active Directory 的 **类型** 。 在 " **名称** " 中输入合适的名称。 单击“应用”。

    ![带有称为 "类型" 和 "名称" 文本框的 "添加集成" 对话框的屏幕截图。](media/starleaf-provisioning-tutorial/image01.png)

3.  然后，将显示 " **SCIM 基本 URL** " 和 " **访问令牌** " 值。 这些值将在 Azure 门户的 StarLeaf 应用程序的 "设置" 选项卡的 " **租户 URL** " 和 " **机密令牌** " 字段中输入。 

    ![带有称为 "类型"、"名称" 和 "SCIM 基 URL" 文本框的 "编辑集成" 对话框的屏幕截图。](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>从库中添加 StarLeaf

若要为 StarLeaf 配置自动用户预 Azure AD 配，需要将 Azure AD 应用程序库中的 StarLeaf 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 StarLeaf，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory** "。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 " **新建应用程序** " 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **StarLeaf** "，在结果面板中选择 " **StarLeaf** "。
    ![结果列表中的 StarLeaf](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>配置 StarLeaf 的自动用户预配

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 StarLeaf 中创建、更新和禁用用户和/或组。

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **StarLeaf** "。

    ![应用程序列表中的 StarLeaf 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5. 在 "管理员凭据" 部分下，输入在 " **租户 url** " 和 " **机密令牌** " 中更早检索到的 **SCIM 基本 URL** 和 **访问令牌** 值。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 StarLeaf。 如果连接失败，请确保 StarLeaf 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在 " **通知电子邮件** " 字段中，输入应接收预配错误通知的人员或组的电子邮件地址，并选中 " **发生故障时发送电子邮件通知** " 复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存** ”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 StarLeaf** "。

    !["映射" 部分的屏幕截图，其中显示了 "将 Azure Active Directory 用户同步到 StarLeaf" 选项。](media/starleaf-provisioning-tutorial/usermapping.png)

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 StarLeaf 的用户属性。 选为 " **匹配** " 属性的特性用于匹配 StarLeaf 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![显示九个映射的 "属性映射" 部分的屏幕截图。](media/starleaf-provisioning-tutorial/userattribute.png)


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。


11. 若要为 StarLeaf 启用 Azure AD 预配服务，请在 " **设置** " 部分中将 " **预配状态** " 更改为 **"打开** "。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 " **设置** " 部分的 " **范围** " 中选择所需的值，定义要预配到 StarLeaf 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 " **同步详细信息** " 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 StarLeaf 执行的所有操作。

有关如何读取 Azure AD 设置日志的详细信息，请参阅 [有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>连接器限制

* StarLeaf 目前不支持组设置。 
* StarLeaf 要求 **电子邮件** 和 **用户名** 值具有相同的源值。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* 了解如何 [查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。
