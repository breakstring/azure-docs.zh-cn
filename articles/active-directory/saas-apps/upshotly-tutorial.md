---
title: 教程：Azure Active Directory 与 Upshotly 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Upshotly 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/5/2021
ms.author: jeedes
ms.openlocfilehash: 53b4702bd4ac5b4fe608eb7a0e161f36d6cb4cb5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726910"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>教程：Azure Active Directory 与 Upshotly 的单一登录 (SSO) 集成

本教程介绍如何将 Upshotly 与 Azure Active Directory (Azure AD) 集成。 将 Upshotly 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Upshotly。
* 让用户使用其 Azure AD 帐户自动登录到 Upshotly。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Upshotly 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Upshotly 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-upshotly-from-the-gallery"></a>从库中添加 Upshotly

若要配置 Upshotly 与 Azure AD 的集成，需要从库中将 Upshotly 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“Upshotly”   。
1. 从结果面板中选择“Upshotly”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-upshotly"></a>配置并测试 Upshotly 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Upshotly 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Upshotly 中的相关用户之间建立链接关系。

若要配置并测试 Upshotly 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Upshotly SSO](#configure-upshotly-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Upshotly 测试用户](#create-upshotly-test-user)** - 在 Upshotly 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在 Upshotly 应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”旁边的编辑/铅笔图标以编辑设置。 

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分，若要在 **IDP** 启动的模式下配置应用程序，则请注意，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > 登录 URL 值不是实际值。 使用实际登录 URL 更新这些值。 你将获取本教程后面介绍的 **companyID** 值。 请联系 [Upshotly 客户端支持团队](mailto:support@upshotly.com)进行查询。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Upshotly”部分中，根据你的需要复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Upshotly 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Upshotly”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-upshotly-sso"></a>配置 Upshotly SSO

1. 若要在 Upshotly 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”。 

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Upshotly”会将你定向到 Upshotly 应用程序。 在此处提供管理员凭据以登录到 Upshotly。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-4。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Upshotly，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Upshotly 公司站点。

1. 单击“用户配置文件”  ，导航到“管理”>“SSO”  并执行以下步骤：

    ![Upshotly 配置](./media/upshotly-tutorial/config1.png)

    a. 复制“公司 ID”值，并在 Azure 门户的“基本 SAML 配置”部分中使用此“公司 ID”值替换“登录 URL”中存在的“公司 ID”值。     

    b. 在写字板中打开从 Azure 门户下载的 **联合元数据 XML**，复制该元数据 XML 的内容并将其粘贴到“XML 元数据”文本框中  。

### <a name="create-upshotly-test-user"></a>创建 Upshotly 测试用户

在本部分中，将在 Upshotly Edge Cloud 中创建一个名为 B.Simon 的测试用户。 与 [Upshotly 客户端支持团队](mailto:support@upshotly.com)协作，在 Upshotly Edge Cloud 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Upshotly 登录 URL，可在其中启动登录流。

* 直接转到 Upshotly 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Upshotly

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Upshotly 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Upshotly。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Upshotly 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。