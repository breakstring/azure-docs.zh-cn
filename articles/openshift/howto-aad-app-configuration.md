---
title: Azure Active Directory Azure Red Hat OpenShift 集成
description: 了解如何创建 Azure AD 安全组和用户，以便在 Microsoft Azure Red Hat OpenShift 群集上测试应用。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ee8613d0300a941f80577c98be106173d5d3ced1
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220697"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory Azure Red Hat OpenShift 集成

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将在年6月 30 2022 日停用。 支持创建新的 Azure Red Hat OpenShift 3.11 群集持续到30年 11 2020 月30日。 停用后，剩余的 Azure Red Hat OpenShift 3.11 群集将关闭，以防出现安全漏洞。
> 
> 按照本指南 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如果有特定问题， [请](mailto:arofeedback@microsoft.com)联系我们。

如果尚未) 租户创建 Azure Active Directory (Azure AD，请按照 [创建 Azure Red Hat OpenShift 的 Azure AD 租户](howto-create-tenant.md) 中的说明操作，然后继续执行这些说明。

Microsoft Azure Red Hat OpenShift 需要权限来代表群集执行任务。 如果你的组织还没有 Azure AD 用户、Azure AD 安全组或 Azure AD 应用注册以用作服务主体，请按照以下说明进行创建。

## <a name="create-a-new-azure-active-directory-user"></a>创建新的 Azure Active Directory 用户

在 [Azure 门户](https://portal.azure.com)中，确保你的租户显示在门户右上方的用户名下：

![如果显示了错误的租户，请在右上方列出了租户的门户屏幕截图 ](./media/howto-create-tenant/tenant-callout.png) ，单击右上方的用户名，然后单击 " **切换目录**"，然后从 " **所有目录** " 列表中选择正确的租户。

创建新的 Azure Active Directory "所有者" 用户登录到 Azure Red Hat OpenShift 群集。

1. 请参阅 " [用户-所有用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) " 边栏选项卡。
2. 单击 " **+ 新建用户** " 以打开 " **用户** " 窗格。
3. 输入此用户的 **名称** 。
4. 基于创建的租户的名称创建 **用户名** ，并  `.onmicrosoft.com` 将其追加到末尾。 例如，`yourUserName@yourTenantName.onmicrosoft.com`。 记下此用户名。 需要用它登录到群集。
5. 单击 " **目录角色** " 以打开 "目录角色" 窗格，选择 " **所有者** "，然后单击窗格底部的 **"确定"** 。
6. 在 " **用户** " 窗格中，单击 " **显示密码** " 并记录临时密码。 首次登录后，系统将提示你重置它。
7. 在窗格底部，单击 " **创建** " 创建用户。

## <a name="create-an-azure-ad-security-group"></a>创建 Azure AD 安全组

若要授予群集管理员访问权限，Azure AD 安全组中的成员身份将同步到 OpenShift 组 "osa-客户管理员"。 如果未指定，将不授予群集管理员访问权限。

1. 打开 " [Azure Active Directory 组](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) " 边栏选项卡。
2. 单击 " **+ 新建组**"。
3. 提供组名称和描述。
4. 将 **组类型** 设置为 **安全**。
5. 设置要**分配**的**成员身份类型**。

    将你在之前步骤中创建的 Azure AD 用户添加到此安全组。

6. 单击 " **成员** "，打开 " **选择成员** " 窗格。
7. 在 "成员" 列表中，选择上面创建的 Azure AD 用户。
8. 在门户底部，单击 " **选择** "，然后单击 " **创建** " 来创建安全组。

    记下 "组 ID" 值。

9. 创建组后，会在所有组的列表中看到它。 单击新组。
10. 在出现的页面上，复制 **对象 ID**。 `GROUPID`在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值。

> [!IMPORTANT]
> 若要将此组与 OpenShift 组进行同步，请使用 Azure CLI 创建群集。 Azure 门户当前缺少用于设置此组的字段。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

通过省略命令的标志，可以在创建群集的过程中自动创建 Azure Active Directory (Azure AD) 应用注册客户端 `--aad-client-app-id` `az openshift create` 。 本教程介绍了如何创建 Azure AD 应用注册以实现完整性。

如果你的组织尚无 Azure Active Directory (Azure AD) 应用注册以用作服务主体，请按照以下说明创建一个。

1. 打开 " [应用注册" 边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) ，然后单击 " **+ 新注册**"。
2. 在 " **注册应用程序** " 窗格中，输入应用程序注册的名称。
3. 确保在 " **受支持的帐户类型** " 下，选中 " **仅限此组织目录中的帐户** "。 这是最安全的选择。
4. 我们会在以后知道群集的 URI 后再添加一个重定向 URI。 单击 " **注册** " 按钮，创建 Azure AD 应用程序注册。
5. 在出现的页面上，向下复制 **应用程序 (客户端) ID**。 `APPID`在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值。

![应用对象页的屏幕截图](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>创建客户端机密

生成用于对应用进行身份验证的客户端机密，以便 Azure Active Directory。

1. 在 "应用注册" 页的 " **管理** " 部分中，单击 " **证书" & 机密**"。
2. 在 " **证书 & 机密** " 窗格上，单击 " **+ 新建客户端密钥**"。  此时将显示 " **添加客户端密码** " 窗格。
3. 提供 **说明**。
4. 将 **过期** 时间设置为所需的持续时间（例如， **在2年内**）。
5. 单击 " **添加** "，密钥值将显示在页面的 " **客户端密钥** " 部分中。
6. 复制密钥值。 `SECRET`在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值。

!["证书和机密" 窗格的屏幕截图](./media/howto-create-tenant/create-key.png)

有关 Azure 应用程序对象的详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

有关创建新的 Azure AD 应用程序的详细信息，请参阅 [使用 Azure Active Directory 1.0 版终结点注册应用](../active-directory/develop/quickstart-register-app.md)。

## <a name="add-api-permissions"></a>添加 API 权限

[//]: # (不要更改为 Microsoft Graph。它不适用于 Microsoft Graph。)
1. 在 "**管理**" 部分单击 " **API 权限**"
2. 单击 " **添加权限** "，然后选择 " **Azure Active Directory 关系图** ，然后 **委派权限**"。
> [!NOTE]
> 请确保已选择 "Azure Active Directory 图形"，而不是 "Microsoft Graph" 磁贴。

3. 在下面的列表中展开 " **用户** "，并启用 " **用户读取** " 权限。 如果在默认情况下启用了 **User. read** ，请确保它是 **Azure Active Directory Graph** 权限 **用户。读取**。
4. 向上滚动并选择 " **应用程序权限**"。
5. 在下面的列表中展开 " **目录** "，然后启用 **ReadAll**。
6. 单击 " **添加权限** " 以接受更改。
7. API 权限面板现在应显示 "ReadAll *" 和 "* *目录*"。 请注意 " *ReadAll*" 旁边的 "**管理员同意要求**" 列中的警告。
8. 如果你是*Azure 订阅管理员*，请单击下面的 "**授予*订阅名称*管理员许可**"。 如果你不是 *Azure 订阅管理员*，请向管理员请求同意。

![API 权限面板的屏幕截图。 添加了 ReadAll 权限，ReadAll 需要管理员许可。](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 只有在授予同意后，才可以同步群集管理员组。 你将看到一个绿色圆圈，其中包含一个复选标记和一条 "*需要管理员同意* *" 列*中的消息。

有关管理管理员和其他角色的详细信息，请参阅 [添加或更改 Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)。

## <a name="resources"></a>资源

* [Azure Active Directory 中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)
* [快速入门：向 Azure Active Directory v1.0 终结点注册应用](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>后续步骤

如果已满足所有 [Azure Red Hat OpenShift 先决条件](howto-setup-environment.md)，则可以创建第一个群集！

尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)