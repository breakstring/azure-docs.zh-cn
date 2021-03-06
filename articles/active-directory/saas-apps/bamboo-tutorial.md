---
title: 教程：Azure Active Directory 与 SAML SSO for Bamboo by resolution GmbH 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAML SSO for Bamboo by resolution GmbH 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: c92295eb3f173d0e050740d0aa38787eff242b39
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623931"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>教程：Azure Active Directory 与 SAML SSO for Bamboo by resolution GmbH 集成

本教程介绍如何将 SAML SSO for Bamboo by resolution GmbH 与 Azure Active Directory (Azure AD) 集成。 将 SAML SSO for Bamboo by resolution GmbH 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAML SSO for Bamboo by resolution GmbH。
* 让用户通过其 Azure AD 帐户自动登录到 SAML SSO for Bamboo by resolution GmbH。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 SAML SSO for Bamboo by resolution GmbH 的集成，需要以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用了 SAML SSO for Bamboo by resolution GmbH 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAML SSO for Bamboo by resolution GmbH 支持 **SP 和 IDP** 发起的 SSO
* SAML SSO for Bamboo by resolution GmbH 支持 **实时** 用户预配

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>从库添加 SAML SSO for Bamboo by resolution GmbH

要配置 SAML SSO for Bamboo by resolution GmbH 的集成（集成到 Azure AD 中），需从库中将 SAML SSO for Bamboo by resolution GmbH 添加到托管 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SAML SSO for Bamboo by resolution GmbH” 。
1. 从结果面板中选择“SAML SSO for Bamboo by resolution GmbH”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>配置并测试 SAML SSO for Bamboo by resolution GmbH 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 SAML SSO for Bamboo by resolution GmbH 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SAML SSO for Bamboo by resolution GmbH 中的相关用户之间建立链接关系。

若要配置并测试 SAML SSO for Bamboo by resolution GmbH 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
     1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
     1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 SAML SSO for Bamboo by resolution GmbH SSO](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SAML SSO for  Bamboo by resolution GmbH 测试用户](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** - 在 SAML SSO for Bamboo by resolution GmbH 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，你将在 Azure 门户中启用 Azure AD SSO。
 
1. 在 Azure 门户中的“SAML SSO for Bamboo by resolution GmbH”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”。 
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

    ![编辑基本 SAML 配置](common/edit-urls.png)
4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

     在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [SAML SSO for Bamboo by resolution GmbH 客户端支持团队](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 SAML SSO for Bamboo by resolution GmbH”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下密码  。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过授予 B.Simon 访问 SAML SSO for Bamboo by resolution GmbH 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择 SAML SSO for bamboo by resolution GmbH。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。
1. 选择“添加用户”。 然后，在“添加分配”对话框中选择“用户和组”。  
1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>配置 SAML SSO for Bamboo by resolution GmbH SSO

1. 以管理员身份登录到 SAML SSO for Bamboo by resolution GmbH 公司站点。

1. 在主工具栏的右侧，单击“设置” > “加载项”。

    ![设置](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. 转到“安全性”部分，在菜单栏上单击“SAML 单一登录”  。

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. 在“SAML 单一登录插件配置”页上，单击“添加 idp”   。

    ![添加 idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. 在“选择 SAML 标识提供者”页上，执行以下步骤  ：

    ![标识提供者](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. 选择“Idp 类型”作为“AZURE AD”   。

    b. 在“名称”文本框中，键入名称  。

    c. 在“说明”文本框中，键入说明  。

    d. 单击“下一步”。 

1. 在“标识提供者配置”页上，单击“下一步”   。

    ![标识配置](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. 在“导入 SAML Idp 元数据”页上，单击“加载文件”以上传从 Azure 门户下载的“元数据 XML”文件    。

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. 单击“下一步”。 

1. 单击“保存设置”。 

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>创建 SAML SSO for Bamboo by resolution GmbH 测试用户

本部分的目的是在 SAML SSO for Bamboo by resolution GmbH 中创建名为“Britta Simon”的用户。 SAML SSO for Bamboo by resolution GmbH 支持实时预配，也可以手动创建用户，具体请根据要求联系 [SAML SSO for Bamboo by resolution GmbH 支持团队](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)。

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SAML SSO for Bamboo by resolution GmbH 登录 URL，你可以从此处启动登录流。  

* 直接转到 SAML SSO for  Bamboo by resolution GmbH 登录 URL，并从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SAML SSO for Bamboo by resolution GmbH。

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 SAML SSO for Bamboo by resolution GmbH 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 SAML SSO for Bamboo by resolution GmbH。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 SAML SSO for Bamboo by resolution GmbH 后，就可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。