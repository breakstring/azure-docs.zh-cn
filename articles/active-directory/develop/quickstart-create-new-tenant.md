---
title: 快速入门：创建一个 Azure Active Directory 租户
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何创建 Azure Active Directory 租户，以便在开发使用 Microsoft 标识平台进行身份验证和授权的应用程序时使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 869b37aea823cf91dc59211b23fcaccd7646afb9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012013"
---
# <a name="quickstart-set-up-a-tenant"></a>快速入门：设置租户

Microsoft 标识平台可让开发人员生成面向各种自定义 Microsoft 365 环境和标识的应用程序。 要开始使用 Microsoft 标识平台，你将需要访问环境（也称为 Azure AD 租户），该环境可以注册和管理应用、可以访问 Microsoft 365 数据并部署自定义条件访问和租户限制。

租户是组织的表示形式。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure、Microsoft Intune 或 Microsoft 365）会收到该实例。

每个 Azure AD 租户都与其他 Azure AD 租户不同并单独存在，而且使用自己的工作和学校标识、消费者标识（如果是 Azure AD B2C 租户）以及应用注册进行表示。 租户内部的应用注册只允许从租户或所有租户的帐户中进行身份验证。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="determining-environment-type"></a>确定环境类型

有两种可以创建的环境类型。 确定所需的环境类型仅基于你的应用将进行身份验证的用户类型。

* 工作和学校（Azure AD 帐户）或 Microsoft 帐户（如 outlook.com 和 live.com）
* 社交和本地帐户（Azure AD B2C）

本快速入门分为两种方案，具体取决于所要生成的应用类型。

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>工作和学校帐户或个人 Microsoft 帐户

### <a name="use-an-existing-tenant"></a>使用现有租户

许多开发人员已通过绑定到 Azure AD 租户的服务或订阅（例如 Microsoft 365 或 Azure 订阅）获得了租户。

1. 若要检查租户，请使用要用于管理应用程序的帐户登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. 查看右上角。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。
   * 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录/租户 ID (GUID) 以及域。
   * 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。

> [!TIP]
> 如果需要查找租户 ID，可执行以下操作：
> * 将鼠标指针悬停在帐户名上以获取目录/租户 ID，或
> * 在 Azure 门户中搜索并选择“Azure Active Directory”>“属性”>“租户 ID”

如果没有任何与帐户关联的现有租户，则帐户名下面会显示一个 GUID；另外，除非按照下一节的步骤操作，否则无法执行注册应用等操作。

### <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

如果还没有 Azure AD 租户或想要创建用于开发的新租户，请参阅[快速入门](../fundamentals/active-directory-access-create-new-tenant.md)，或者只需按照[目录创建体验](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)进行操作。 必须提供以下信息才能创建新租户：

- 组织名称
- **初始域** - 这将包括在 *.onmicrosoft.com 中。 稍后你可以更详细地自定义域。
- 国家或地区

> [!NOTE]
> 对租户进行命名时，请使用字母数字字符。 不允许使用特殊字符。 名称不得超过 256 个字符。

## <a name="social-and-local-accounts"></a>社交和本地帐户

要开始生成登录社交和本地帐户的应用，你将需要创建 Azure AD B2C 租户。 请从[创建 Azure AD B2C 租户](../../active-directory-b2c/tutorial-create-tenant.md)开始。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [注册应用](quickstart-register-app.md)以与 Microsoft 标识平台集成。
