---
title: 以编程方式创建 Azure 订阅
description: 本文可帮助你了解可用于以编程方式创建 Azure 订阅的选项。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493882"
---
# <a name="create-azure-subscriptions-programmatically"></a>以编程方式创建 Azure 订阅

本文可帮助你了解可用于以编程方式创建 Azure 订阅的选项。

使用各种 REST API，可为以下 Azure 协议类型创建订阅：

- 企业协议 (EA)
- Microsoft 客户协议 (MCA)
- Microsoft 合作伙伴协议 (MPA)

不能通过 REST API 以编程方式为其他协议类型创建额外订阅。

对于不同的协议和 API 版本，创建订阅的要求和详细信息也不同。 请参阅以下适用于你的情况的文章：

最新 API：

- [创建 EA 订阅](programmatically-create-subscription-enterprise-agreement.md)
- [创建 MCA 订阅](programmatically-create-subscription-microsoft-customer-agreement.md)
- [创建 MPA 订阅](programmatically-create-subscription-microsoft-partner-agreement.md)

如果你仍在使用[预览 API](programmatically-create-subscription-preview.md)，可继续使用它们创建订阅。 

此外，可[使用 ARM 模板创建订阅](create-subscription-template.md)。 可借助 ARM 模板使用 REST API 自动执行订阅创建过程。 

## <a name="next-steps"></a>后续步骤

* 创建订阅以后，可将该权限授予其他用户和服务主体。 有关详细信息，请参阅[授予创建 Azure Enterprise 订阅（预览版）所需的访问权限](grant-access-to-create-subscription.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](../../governance/management-groups/overview.md)。
