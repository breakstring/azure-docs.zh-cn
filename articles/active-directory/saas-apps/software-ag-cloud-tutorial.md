---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Software AG Cloud 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Software AG Cloud 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Software AG Cloud 集成

本教程介绍如何将 Software AG Cloud 与 Azure Active Directory (Azure AD) 集成。 将 Software AG Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Software AG Cloud。
* 让用户能够使用其 Azure AD 帐户自动登录到 Software AG Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Software AG Cloud 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Software AG Cloud 支持 SP 发起的 SSO
* Software AG Cloud 支持实时用户预配

## <a name="adding-software-ag-cloud-from-the-gallery"></a>从库中添加 Software AG Cloud

若要配置 Software AG Cloud 与 Azure AD 的集成，需要从库中将 Software AG Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Software AG Cloud” 。
1. 从结果面板中选择“Software AG Cloud”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>配置并测试 Software AG Cloud 的 Azure AD SSO

配置 Software AG Cloud 的 Azure AD SSO，并使用名为 B.Simon 的测试用户来对其进行测试。 若要正常使用 SSO，需要在 Azure AD 用户与 Software AG Cloud 中的相关用户之间建立链接关系。

若要配置并测试 Software AG Cloud 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Software AG Cloud SSO](#configure-software-ag-cloud-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Software AG Cloud 测试用户](#create-software-ag-cloud-test-user) - 在 Software AG Cloud 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Software AG Cloud”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Software AG Cloud 客户端支持团队](mailto:support@softwareag.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Software AG Cloud”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Software AG Cloud 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Software AG Cloud”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-software-ag-cloud-sso"></a>配置 Software AG Cloud SSO

1. 在其他 Web 浏览器窗口中，以管理员身份登录到 Software AG Cloud 网站。

1.  单击“管理”

    ![配置 Software AG Cloud 管理](./media/software-ag-cloud-tutorial/admin.png)

1. 请转到“单一登录”>“添加标识提供者”

    ![配置 Software AG Cloud 标识提供者](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. 在下面的页中执行以下步骤。

    ![配置 Software AG Cloud 执行步骤](./media/software-ag-cloud-tutorial/saml-1.png)

    a. 在“标识提供者显示名称”文本框中，提供名称（例如 `azure ad`）。

    b. 在“用于 Software AG Cloud 重定向 URI 的标识提供者唯一标识符”文本框中，输入标识提供者的唯一名称。 “Software AG Cloud 重定向 URI”字段将刷新并使用 URI 进行填充。 复制此 URI，并使用它根据定义的模式在 Azure 门户中配置实体 ID 和其他信息。

    c. 在“标识提供者配置”中导入“联合元数据 XML”文件，然后单击“下一步”  。

    d. 转到“配置”页上，根据需要填写字段。

### <a name="create-software-ag-cloud-test-user"></a>创建 Software AG Cloud 测试用户

在本部分中，将在 Software AG Cloud 中创建名为 Britta Simon 的用户。 Software AG Cloud 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Software AG Cloud 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

假设已将 Microsoft Azure 配置为 Software AG Cloud 中的提供者，请导航至 `www.softwareag.cloud` 并单击“登录”按钮，然后输入环境名称。 在下一个屏幕中，单击“使用 <IDP NAME> 登录”链接并输入凭据。 经过身份验证后，你将登录并转到 Software AG Cloud 主页。

## <a name="next-steps"></a>后续步骤

配置 Software AG Cloud 后，可以强制实施会话控制，以实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。