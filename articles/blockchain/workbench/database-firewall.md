---
title: 配置 Azure 区块链工作台数据库防火墙
description: 了解如何配置 Azure 区块链工作台预览版数据库防火墙，以允许外部客户端和应用程序进行连接。
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009443"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>配置 Azure Blockchain Workbench 数据库防火墙

本文演示如何使用 Azure 门户配置防火墙规则。 防火墙规则允许外部客户端或应用程序连接到 Azure Blockchain Workbench 数据库。

## <a name="connect-to-the-blockchain-workbench-database"></a>连接到 Blockchain Workbench 数据库

若要连接到要配置规则的数据库，请执行以下操作：

1. 使用对 Azure 区块链工作台资源拥有 **所有者**     权限的帐户登录到 Azure 门户。
2. 在左侧导航窗格中，选择“资源组”  。
3. 选择你的 Blockchain Workbench 部署的资源组的名称。
4. 选择“类型”，对资源列表进行排序，然后选择 **SQL Server**。
5. 以下屏幕截图中的资源列表示例演示两个数据库：*master* 和 *lsgn-sdk*。 在 *lsgn-sdk* 上配置防火墙规则。

![列出 Blockchain Workbench 资源](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>创建数据库防火墙规则

若要创建防火墙规则，请执行以下操作：

1. 选择“lsgn-sdk”数据库的链接。
2. 在菜单栏上，选择“设置服务器防火墙”。

   ![设置服务器防火墙](./media/database-firewall/configure-server-firewall.png)

3. 若要为组织创建规则，请执行以下操作：

   * 输入 **规则名称**
   * 输入地址范围的 **起始 IP** 的 IP 地址
   * 输入地址范围的 **结束 IP** 的 IP 地址

   ![创建防火墙规则](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > 如果只想添加计算机的 IP 地址，请选择“+ 添加客户端 IP”。
        
1. 若要保存防火墙配置，请选择“保存”。
2. 通过从应用程序或工具连接，测试为数据库配置的 IP 地址范围。 例如，SQL Server Management Studio。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的数据库视图](database-views.md)