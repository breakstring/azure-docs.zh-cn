---
title: 查看 Azure 预留购买和退款交易
description: 了解如何查看 Azure 预留购买和退款交易。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b986aa2bfce203be85adbcde8e2966c167bf7ca1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151773"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>查看预留购买和退款交易

有几种不同方法可以查看预留购买和退款交易。 可以使用 Azure 门户、Power BI 和 REST API。

## <a name="view-reservation-transactions-in-the-azure-portal"></a>在 Azure 门户中查看预留交易

企业注册或 Microsoft 客户协议计费管理员可以在成本管理和计费中查看预留交易。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。
1. 选择“预留交易”。
1. 若要筛选结果，请选择“时间范围”、“类型”或“说明”。  
1. 选择“应用”。

[![显示 Azure 门户中的预留交易的屏幕截图](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>在 Power BI 中查看预留交易

企业注册或 Microsoft 客户协议计费管理员可以通过成本管理 Power BI 应用查看预留交易。

1. 获取[成本管理 Power BI 应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)。
1. 导航到 RI 购买报表。

[![显示预留交易的示例](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

若要了解详细信息，请参阅[适用于企业协议的 Azure 成本管理 Power BI 应用](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md)。

## <a name="use-apis-to-get-reservation-transactions"></a>使用 API 获取预留交易

企业协议 (EA) 和 Microsoft 客户协议用户可以使用[预留交易 - 列表 API](/rest/api/consumption/reservationtransactions/list) 获取预留交易数据。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](manage-reserved-vm-instance.md)