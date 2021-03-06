---
title: 如何扫描 Azure 数据资源管理器
description: 本操作方法指南介绍了如何扫描 Azure 数据资源管理器的详细信息。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896086"
---
# <a name="register-and-scan-azure-data-explorer"></a>注册并扫描 Azure 数据资源管理器

本文概述了如何在 Azure 监控范围中注册 Azure 数据资源管理器帐户并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure 数据资源管理器支持完整和增量扫描来捕获元数据和架构。 扫描还会根据系统和自定义分类规则自动对数据进行分类。

## <a name="prerequisites"></a>必备条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 你需要成为 Azure 监控范围数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

仅有一种方法可以设置 Azure 数据资源管理器的身份验证：

- Service Principal

### <a name="service-principal"></a>服务主体

若要将服务主体身份验证用于扫描，可以使用现有的身份验证，也可以创建一个新的身份验证。 

> [!Note]
> 如果必须创建新的服务主体，请执行以下步骤：
> 1. 导航到 [Azure 门户](https://portal.azure.com)。
> 1. 从左侧菜单中选择“Azure Active Directory”。
> 1. 选择 **“应用注册”**。
> 1. 选择“+ 新建应用程序注册”。
> 1. 为应用程序输入名称（服务主体名称）。
> 1. 选择“仅此组织目录中的帐户”。
> 1. 对于重定向 URI，选择“Web”并输入所需的任何 URL；它无需是真实的 URL 或有效的 URL。
> 1. 然后选择“注册”。

需要获取服务主体的应用程序 ID 和机密：

1. 导航到 [Azure 门户](https://portal.azure.com)中的服务主体
1. 复制“概述”中的“应用程序(客户端) ID”和“证书和机密”中的“客户端机密”的值   。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并输入所选的“名称”和“值”作为服务主体的“客户端机密”   
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>向服务主体授予对 Azure 数据资源管理器实例的访问权限

1. 导航到 Azure 门户。 然后导航到 Azure 数据资源管理器实例。

1. 在 "**权限**" 选项卡中将服务主体添加到 **AllDatabasesViewer** 角色，如以下屏幕截图所示。

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="用于在权限中添加服务主体的屏幕截图" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>注册 Azure 数据资源管理器帐户

若要在数据目录中注册新的 Azure 数据资源管理器 (Kusto) 帐户，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航区域中选择“源”
1. 选择“注册”
1. 在 " **注册源**" 中，选择 " **Azure 数据资源管理器**
1. 选择“继续”

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="注册新数据源" border="true":::

在 " **注册源 (Azure 数据资源管理器 (Kusto) # B3** 屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
1. 选择要指向所需存储帐户的方式：
   1. 选择 " **从 azure** 订阅"，从 " **azure 订阅** " 下拉框中选择适当的订阅，并从 " **群集** " 下拉框中选择相应的群集。
   1. 或者，可以选择 " **手动输入** "，然后输入服务终结点 (URL) 。
1. 选择“完成”以注册数据源。

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="注册源选项" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
