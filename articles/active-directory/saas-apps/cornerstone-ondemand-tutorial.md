---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Cornerstone OnDemand 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Cornerstone OnDemand 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0994126c4e31d880a792904b0c086a56f6bd90fb
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727719"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Cornerstone OnDemand 的集成

本教程介绍如何将 Cornerstone OnDemand 与 Azure Active Directory (Azure AD) 集成。 将 Cornerstone OnDemand 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cornerstone OnDemand。
* 让用户通过其 Azure AD 帐户自动登录到 Cornerstone OnDemand。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Cornerstone OnDemand 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Cornerstone OnDemand 支持 **SP** 发起的 SSO
* Cornerstone OnDemand 支持[自动用户预配](cornerstone-ondemand-provisioning-tutorial.md)


## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>从库中添加 Cornerstone OnDemand

若要配置 Cornerstone OnDemand 与 Azure AD 的集成，需要从库中将 Cornerstone OnDemand 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Cornerstone OnDemand”。
1. 从结果面板中选择“Cornerstone OnDemand”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-ondemand"></a>为 Cornerstone OnDemand 配置和测试 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Cornerstone OnDemand 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Cornerstone OnDemand 相关用户之间建立链接关系。

若要配置并测试 Cornerstone OnDemand 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Cornerstone OnDemand SSO](#configure-cornerstone-ondemand-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Cornerstone OnDemand 测试用户](#create-cornerstone-ondemand-test-user)** - 在 Cornerstone OnDemand 中创建 B.Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Cornerstone OnDemand”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置。 

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company>.csod.com/samldefault.aspx?ouid=2` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<company>.csod.com/samldefault.aspx?ouid=2`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Cornerstone OnDemand 客户端支持团队](mailto:moreinfo@csod.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Cornerstone OnDemand”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
    1. 在“名称”字段中，输入 `B.Simon`。  
    1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
    1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Cornerstone OnDemand 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Cornerstone OnDemand”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cornerstone-ondemand-sso"></a>配置 Cornerstone OnDemand SSO

若要在 **Cornerstone OnDemand** 端配置单一登录，需要将下载的 **证书(Base64)** 以及从 Azure 门户复制的相应 URL 发送给 [Cornerstone OnDemand 支持团队](mailto:moreinfo@csod.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-cornerstone-ondemand-test-user"></a>创建 Cornerstone OnDemand 测试用户

本部分的目的是在 Cornerstone OnDemand 中创建名为“B.Simon”的用户。 Cornerstone OnDemand 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](./cornerstone-ondemand-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

若要配置用户预配，请将要预配的 Azure AD 用户相关信息（例如：名字、电子邮件）发送给 [Cornerstone OnDemand 支持团队](mailto:moreinfo@csod.com)。

>[!NOTE]
>可使用 Cornerstone OnDemand 提供的任何其他 Cornerstone OnDemand 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Cornerstone OnDemand 登录 URL，可以从那里启动登录流。 

* 直接转到 Cornerstone OnDemand 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Cornerstone OnDemand”磁贴时，这会重定向到“Cornerstone OnDemand 登录 URL”。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Cornerstone OnDemand 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)