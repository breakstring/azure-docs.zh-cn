---
title: 教程：Azure Active Directory 与 JLL TRIRIGA 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 JLL TRIRIGA 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: b33db1b766d99aeb8d0a08f6396b5384948f118e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736863"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jll-tririga"></a>教程：Azure Active Directory 与 JLL TRIRIGA 的单一登录 (SSO) 集成

本教程介绍如何将 JLL TRIRIGA 与 Azure Active Directory (Azure AD) 集成。 将 JLL TRIRIGA 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 JLL TRIRIGA。
* 让用户使用其 Azure AD 帐户自动登录到 JLL TRIRIGA。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 JLL TRIRIGA 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* JLL TRIRIGA 支持 IDP 发起的 SSO

## <a name="adding-jll-tririga-from-the-gallery"></a>从库中添加 JLL TRIRIGA

若要配置 JLL TRIRIGA 与 Azure AD 的集成，需要从库中将 JLL TRIRIGA 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中键入“JLL TRIRIGA” 。
1. 在结果面板中选择“JLL TRIRIGA”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-jll-tririga"></a>配置并测试 JLL TRIRIGA 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 JLL TRIRIGA 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 JLL TRIRIGA 中的相关用户之间建立链接关系。

若要配置并测试 JLL TRIRIGA 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 JLL TRIRIGA SSO](#configure-jll-tririga-sso) - 在应用程序端配置单一登录设置。
    1. [创建 JLL TRIRIGA 测试用户](#create-jll-tririga-test-user) - 在 JLL TRIRIGA 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“JLL TRIRIGA”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”文本框中，使用以下模式之一键入 URL：
   
    | 标识符 |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT` |
    | `https://<SUBDOMAIN>.jll.com` |
    |

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | 回复 URL |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT/samlsps/trisaml` |
    | `https://<SUBDOMAIN>.jll.com/samlsps/trisaml` |
    |

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [JLL TRIRIGA 客户端支持团队](https://www.us.jll.com/contact-us)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 JLL TRIRIGA”部分中，根据要求复制相应的 URL。

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

本部分将通过向 B.Simon 授予对 JLL TRIRIGA 的访问权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“JLL TRIRIGA”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-jll-tririga-sso"></a>配置 JLL TRIRIGA SSO

若要在 JLL TRIRIGA 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [JLL TRIRIGA 支持团队](https://www.us.jll.com/contact-us) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-jll-tririga-test-user"></a>创建 JLL TRIRIGA 测试用户

本部分将在 JLL TRIRIGA 中创建名为 Britta Simon 的用户。 与 [JLL TRIRIGA 支持团队](https://www.us.jll.com/contact-us)协作，在 JLL TRIRIGA 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”，随后应会自动登录到为其设置了 SSO 的 JLL TRIRIGA

* 可以使用 Microsoft 访问面板。 在访问面板中单击 JLL TRIRIGA 磁贴时，应会自动登录到为其设置了 SSO 的 JLL TRIRIGA。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 JLL TRIRIGA 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。