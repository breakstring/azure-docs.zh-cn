---
title: 在 Azure Active Directory 中配置命名位置 | Microsoft Docs
description: 了解如何配置命名位置。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f4d17596936dd9d0ebbdae3c351cac9ed2a570
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89299858"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中配置命名位置

通过命名位置，可在组织中标记受信任的 IP 地址范围。 Azure AD 使用命名位置以：
- 检测[风险检测](../identity-protection/overview-identity-protection.md)中的误报。 从受信任的位置登录可降低用户的登录风险。   
- 配置[基于位置的条件访问](../conditional-access/location-condition.md)。

在本快速入门中，你将了解如何在环境中配置命名位置。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要以下项：

* Azure AD 租户。 注册[免费试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。 
* 一个身份为租户的全局管理员的用户。
* 一个已建立且在组织中受到信任的 IP 范围。 IP 范围需采用**无类别域际路由 (CIDR)** 格式。

## <a name="configure-named-locations"></a>配置命名位置

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧窗格中，选择“Azure Active Directory”，然后从“安全性”部分中选择“条件访问”。

    ![“条件访问”选项卡](./media/quickstart-configure-named-locations/entrypoint.png)

3. 在“条件访问”页上，选择“命名位置”，然后选择“新位置”。

    ![命名位置](./media/quickstart-configure-named-locations/namedlocation.png)

6. 填写新页面上的表单。 

   * 在“名称”框中，键入命名位置的名称。
   * 在“IP 范围”框中，键入 CIDR 格式的 IP 范围。  
   * 单击**创建**。
    
     ![新建边栏选项卡](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [条件访问中条件的位置](../conditional-access/concept-conditional-access-conditions.md#locations)。
- [风险登录报表](../identity-protection/overview-identity-protection.md)。