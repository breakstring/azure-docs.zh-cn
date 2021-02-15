---
title: Azure 应用程序托管应用程序产品/服务发布指南-Azure Marketplace
description: 本文介绍在 Azure Marketplace 中发布托管应用程序的要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: d4fb3354b7035149b80191528b2f5335b593b764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433540"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 托管应用程序的发布指南

Azure *托管应用程序* 产品/服务是在 azure Marketplace 中发布 azure 应用程序的一种方法。 托管应用程序是通过 Azure Marketplace 部署并计费的服务。 用户看到的列表选项 *现已获取*。

本文介绍了托管应用程序产品/服务类型的要求。

在以下条件下使用托管应用程序产品/服务类型：

- 你要为客户部署基于订阅的解决方案，方法是使用虚拟机 (VM) 或整个基础结构即服务 (IaaS 基于) 的解决方案。
- 你或你的客户需要由合作伙伴管理解决方案。

>[!NOTE]
>例如，合作伙伴可以是系统集成商或托管服务提供商 (MSP) 。  

## <a name="managed-application-offer-requirements"></a>托管应用程序提供要求

|要求 |详细信息  |
|---------|---------|
|Azure 订阅 | 托管应用程序必须部署到客户的订阅中，但可以由第三方管理。 |
|计费和计量    |  客户的 Azure 订阅中提供了资源。 使用即用即付支付模型的 Vm 通过 Microsoft 与客户进行交易，并通过客户的 Azure 订阅进行计费。 <br><br> 对于自带许可证 Vm，Microsoft 会对客户订阅中产生的任何基础结构成本计费，但你直接向客户收取软件许可费用。        |
|与 Azure 兼容的虚拟硬盘 (VHD)     |   必须基于 Windows 或 Linux 构建 VM。<br><br>有关创建 Linux VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](../virtual-machines/linux/endorsed-distros.md)。<br><br>有关创建 Windows VHD 的详细信息，请参阅 [创建 Azure 应用程序产品/服务](./create-new-azure-apps-offer.md)。 |

---

> [!NOTE]
> 必须通过 Azure Marketplace 部署托管应用程序。 如果客户沟通是个问题，请在启用 "潜在客户共享" 后与感兴趣的客户联系。  

> [!Note]
> 现在提供了云解决方案提供商) 合作伙伴渠道选择加入 (CSP。 有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息，请参阅 [云解决方案提供商](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[使用 Azure 市场发展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 有关详细信息，请参阅 [创建 Azure 应用程序产品/服务](./create-new-azure-apps-offer.md) 。
