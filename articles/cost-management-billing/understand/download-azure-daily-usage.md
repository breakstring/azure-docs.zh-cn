---
title: 查看和下载 Azure 使用情况与费用
description: 了解如何下载或查看 Azure 每日使用情况和费用，并查看其他可用资源。
keywords: 计费使用情况, 使用费, 使用情况下载, 查看使用情况, Azure 发票, Azure 使用情况
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 141da3dfab9996110952ec266733271582e66ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439194"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>查看和下载 Azure 使用情况与费用

可以在 Azure 门户中下载 Azure 使用情况和费用的每日明细。 只有某些角色（例如帐户管理员或企业管理员）有权获取 Azure 使用情况信息。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](../manage/manage-billing-access.md)。

如果签订了 Microsoft 客户协议 (MCA)，则必须是计费对象信息所有者、参与者、读者或发票管理者，才能查看 Azure 使用情况和费用。  如果签订了 Microsoft 合作伙伴协议 (MPA)，则只有合作伙伴组织中的全局管理员和管理员代理角色才能查看和下载 Azure 使用情况和费用。 [在 Azure 门户中检查计费帐户类型](#check-your-billing-account-type)。

根据使用的订阅类型，用于下载使用情况和费用的选项有所不同。

## <a name="download-usage-from-the-azure-portal-csv"></a>从 Azure 门户下载使用情况 (.csv)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![屏幕截图显示在 Azure 门户中搜索“成本管理 + 计费”。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. 根据你的访问权限，可能需要选择一个计费帐户或计费对象信息。
1. 在左侧菜单中，选择“账单”  下的“发票”  。
1. 在发票网格中，找到与要下载的使用情况对应的计费周期行。
1. 选择右侧的下载图标或省略号 (`...`)  。  
  ![屏幕截图显示带有下载选项的“成本管理 + 计费发票”页面。](./media/download-azure-daily-usage/download-usage-others.png)  
1. “下载”窗格将在右侧打开。 在“使用情况详细信息”部分，选择“下载”   。  

## <a name="download-usage-for-ea-customers"></a>下载 EA 客户的使用情况数据

若要作为 EA 客户查看和下载使用情况数据，你必须是启用了“查看费用策略”的企业管理员、帐户所有者或部门管理员。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![屏幕截图显示 Azure 门户搜索。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. 选择“使用情况 + 费用”。
1. 针对要下载的月份，选择“下载”。  
    ![屏幕截图显示 EA 客户的“成本管理 + 计费发票”页面。](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>下载未决费用的使用情况

如果签订了 Microsoft 客户协议，则可以下载当前计费周期的每月至今使用量。 这些尚未计费的使用费。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”。
3. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
4. 在“概述”区域中，找到“最近费用”下的下载链接。
5. 选择“下载使用量和价格”。  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="显示从“概述”下载的屏幕截图" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>检查计费帐户类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和使用费用，请参阅：

- [了解 Microsoft Azure 详细使用条款](understand-usage.md)
- [了解 Microsoft Azure 账单](review-individual-bill.md)
- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载组织的 Azure 定价](../manage/ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解有关 Microsoft 客户协议 Azure 详细使用情况的术语](mca-understand-your-usage.md)
- [了解 Microsoft 客户协议发票上的费用](review-customer-agreement-bill.md)
- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载 Microsoft 客户协议税单](mca-download-tax-document.md)
- [查看和下载组织的 Azure 定价](../manage/ea-pricing.md)
