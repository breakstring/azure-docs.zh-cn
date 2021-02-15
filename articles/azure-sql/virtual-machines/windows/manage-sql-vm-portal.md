---
title: 使用 Azure 门户管理 Azure 中 SQL Server 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 门户中访问 Azure 上托管的 SQL Server VM 的 SQL 虚拟机资源。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 68c7805136a7361a64a6ff6dfbb9e7d910b2ea9b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357936"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 中的 SQL Server VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 [Azure 门户](https://portal.azure.com)中， [**SQL 虚拟机**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源是一种独立的管理服务，用于在 Azure vm 上管理 SQL Server。 可以使用它同时查看所有 SQL Server VM，以及修改专用于 SQL Server 的设置： 

![SQL 虚拟机资源](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>备注

- 建议使用 [**SQL 虚拟机**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源来查看和管理 Azure 中的 SQL Server vm。 但，SQL 虚拟机资源目前不支持管理[已终止支持](sql-server-2008-extend-end-of-support.md)的 SQL Server VM。 要管理已终止支持的 SQL Server VM 的设置，请改用已弃用的 [SQL Server 配置选项卡](#access-the-sql-server-configuration-tab)。 
- **Sql 虚拟机** 资源仅适用于已 [注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)的 SQL Server vm。 


## <a name="access-the-sql-virtual-machines-resource"></a>访问 SQL 虚拟机资源
要访问 SQL 虚拟机资源，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”。 
1. 在搜索框中输入“SQL 虚拟机”。
1. （可选）：选择“SQL 虚拟机”旁边的星号将此选项添加到“收藏夹”菜单 。 
1. 选择“SQL 虚拟机”。 

   ![在所有服务中查找 SQL Server 虚拟机](./media/manage-sql-vm-portal/sql-vm-search.png)

1. 该门户列出订阅内所有可用的 SQL Server VM。 选择要管理的那一个，以打开“SQL 虚拟机”资源。 如果未显示你的 SQL Server VM，请使用搜索框。 

   ![所有可用的 SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   选择你的 SQL Server VM 可打开该 SQL 虚拟机资源： 


   ![查看 SQL 虚拟机资源](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> SQL 虚拟机资源用于专用的 SQL Server 设置。 选择“虚拟机”框中的 VM 名称，以打开特定于该 VM 但不专用于 SQL Server 的设置。 

## <a name="access-the-sql-server-configuration-tab"></a>访问 SQL Server 配置选项卡
“SQL Server 配置”选项卡已被弃用。 目前，这是唯一一种方法，用于管理 SQL Server Vm 的 [结尾](sql-server-2008-extend-end-of-support.md) ，以及 SQL Server 未 [注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)中的 vm。

要访问已弃用的“SQL Server 配置”选项卡，请转到“虚拟机”资源 。 请使用以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”。 
1. 在搜索框中输入“虚拟机”。
1. （可选）：选择“虚拟机”旁边的星号将此选项添加到“收藏夹”菜单 。 
1. 选择“虚拟机”。 

   ![搜索虚拟机](./media/manage-sql-vm-portal/vm-search.png)

1. 门户列出订阅中的所有虚拟机。 选择要管理的那一个，以打开“虚拟机”资源。 如果未显示你的 SQL Server VM，请使用搜索框。 
1. 在“设置”窗格中选择“SQL Server 配置”，以管理 SQL Server VM 。 

   ![SQL Server 配置](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)


