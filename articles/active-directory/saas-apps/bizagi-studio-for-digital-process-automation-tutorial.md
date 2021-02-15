---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Bizagi for Digital Process Automation 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Bizagi for Digital Process Automation 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457055"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Bizagi for Digital Process Automation 的集成

本教程介绍如何将 Bizagi for Digital Process Automation 服务或服务器与 Azure Active Directory (Azure AD) 集成。 将 Bizagi for Digital Process Automation 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Bizagi for Digital Process Automation 服务或服务器项目。
* 让用户使用其 Azure AD 帐户自动登录到 Bizagi for Digital Process Automation 服务或服务器项目。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 使用自动化服务或服务器的 Bizagi 项目。 
* 拥有自己的 SAML 断言签名证书。 此证书必须以 p12 或 pfx 格式生成。
* 拥有从 Bizagi 项目生成的 XML 格式的元数据文件。 

## <a name="scenario-description"></a>方案描述

本教程在使用自动化服务或服务器的 Bizagi 项目中配置并测试 Azure AD SSO。

* Bizagi for Digital Process Automation 支持 SP 发起的 SSO
* 配置 Bizagi for Digital Process Automation 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>从库中添加 Bizagi or Digital Process Automation

若要配置 Bizagi for Digital Process Automation 与 Azure AD 的集成，需要从库中将 Bizagi for Digital Process Automation 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Bizagi for Digital Process Automation” 。
1. 在结果面板中选择“Bizagi for Digital Process Automation”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>配置并测试 Bizagi for Digital Process Automation 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Bizagi for Digital Process Automation 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Bizagi 项目中的相关用户之间建立关联。

若要配置并测试 Bizagi for Digital Process Automation 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Bizagi for Digital Process Automation SSO](#configure-bizagi-for-digital-process-automation-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Bizagi for Digital Process Automation 测试用户](#create-bizagi-for-digital-process-automation-test-user)** - 在 Bizagi for Digital Process Automation 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Bizagi for Digital Process Automation”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“上传元数据文件”选项中上传 Bizagi 元数据文件。
1. 查看配置。 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入 Bizagi 项目的 URL：`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. 在“标识符(实体 ID)”文本框中，键入 Bizagi 项目的 URL：`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Bizagi for Digital Process Automation 支持团队](mailto:jarvein.rivera@bizagi.com)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)
    
    此元数据 URL 必须在 Bizagi 项目的身份验证选项中注册。
    
1. 在“设置 SAML 单一登录”页上，单击“用户属性和声明”的编辑/笔形图标以编辑唯一用户标识符 。
    
    将唯一用户标识符设置为 user.mail。

### <a name="create-an-azure-ad-test"></a>创建 Azure AD 测试 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Bizagi for Digital Process Automation 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Bizagi for Digital Process Automation”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>配置 Bizagi for Digital Process Automation SSO

若要在 Bizagi for Digital Process Automation 端配置单一登录，需要将“应用联合元数据 URL”发送给 [Bizagi for Digital Process Automation 支持团队](mailto:jarvein.rivera@bizagi.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>创建 Bizagi for Digital Process Automation 测试用户

在本部分中，将在 Bizagi for Digital Process Automation 中创建一个名为 Britta Simon 的用户。 在 [Bizagi for Digital Process Automation 支持团队](mailto:jarvein.rivera@bizagi.com)的配合下，将用户添加到 Bizagi for Digital Process Automation 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Bizagi for Digital Process Automation 磁贴时，应当会自动登录到为其设置了 SSO 的 Bizagi for Digital Process Automation 的门户。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [在 Azure AD 中试用 Bizagi for Digital Process Automation](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)