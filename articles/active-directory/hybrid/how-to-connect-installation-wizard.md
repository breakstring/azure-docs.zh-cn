---
title: 重新运行 Azure AD Connect 安装向导 | Microsoft 文档
description: 介绍安装向导第二次运行时的工作原理。
keywords: Azure AD Connect 安装向导允许在第二次运行它时配置维护设置
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306117"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect 同步：第二次运行安装向导
首次运行 Azure AD Connect 安装向导时，该向导将逐步引导用户配置安装。 如果再次运行安装向导，它会提供维护选项。

>[!IMPORTANT]
>请注意，正在进行同步时，无法运行安装向导。  请在启动向导之前验证同步是否未运行。

可以在“开始”菜单中找到名为 **Azure AD Connect** 的安装向导。

![“开始”菜单](./media/how-to-connect-installation-wizard/startmenu.png)

启动安装向导时，将看到包含以下选项的页：

![列出其他任务的页面](./media/how-to-connect-installation-wizard/additionaltasks.png)

如果已使用 Azure AD Connect 安装 ADFS，则会有更多选项。 [ADFS 管理](how-to-connect-fed-management.md#manage-ad-fs)中介绍了其他 ADFS 选项。

请选择其中一项任务，并单击“下一步”继续。 

> [!IMPORTANT]
> 打开安装向导时，同步引擎中的所有操作都会挂起。 请确保在完成配置更改后，立即关闭安装向导。
>
>

## <a name="view-current-configuration"></a>查看当前配置
此选项可让你快速查看当前配置的选项。

![列出所有选项及其状态的页面](./media/how-to-connect-installation-wizard/viewconfig.png)

单击“上一步”可以后退。  如果选择“退出”，将关闭安装向导。 

## <a name="customize-synchronization-options"></a>自定义同步选项
此选项可用于更改同步配置。 将在自定义配置安装路径中看到一部分选项。 即使一开始是使用快速安装也会看到此选项。

* [添加更多目录](how-to-connect-install-custom.md#connect-your-directories)。 若要删除目录，请参阅[删除连接器](how-to-connect-sync-service-manager-ui-connectors.md#delete)。
* [更改域和 OU 筛选](how-to-connect-install-custom.md#domain-and-ou-filtering)。
* 删除组筛选。
* [更改可选功能](how-to-connect-install-custom.md#optional-features)。

初始安装中的其他选项既无法更改，也不能使用。 这些选项包括：

* 更改用于 userPrincipalName 和 sourceAnchor 的属性。
* 更改不同林中对象的联接方法。
* 启用基于组的筛选。

## <a name="refresh-directory-schema"></a>刷新目录架构
如果已更改其中一个本地 AD DS 林中的架构，则应使用此选项。 例如，可能已安装 Exchange，或升级到包含设备对象的 Windows Server 2012 架构。 在这种情况下，需指示 Azure AD Connect 从 AD DS 再次读取架构并更新其缓存。 此操作还会重新生成同步规则。 举例来说，如果添加 Exchange 架构，配置中就会添加 Exchange 的同步规则。

选择此选项时，将列出配置中的所有目录。 可以保留默认设置，并刷新所有林或取消选择其中某些林。

![列出环境中所有目录的页面](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>配置过渡模式
此选项可让你启用和禁用服务器上的过渡模式。 有关暂存模式及其使用方式的详细信息可在[操作](how-to-connect-sync-staging-server.md)中找到。

此选项显示过渡模式当前是已启用还是已禁用：  
![显示已禁用暂存模式的屏幕截图。](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

若要更改状态，请选择此选项，并选中或取消选中复选框。  
![同时显示过渡模式当前状态的选项](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>更改用户登录
通过此选项，可将用户登录方式改为密码哈希同步、直通身份验证或联合。 但不能更改为“不配置”。 

有关此选项的详细信息，请参阅[用户登录](plan-connect-user-signin.md#changing-the-user-sign-in-method)。

## <a name="next-steps"></a>后续步骤
* 在[了解声明性设置](concept-azure-ad-connect-sync-declarative-provisioning.md)中了解 Azure AD Connect 同步使用的配置模型。

**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
