---
title: 在订阅者与 CSP 之间转移 Azure 订阅
description: 了解如何在订阅者与 CSP 之间转移 Azure 订阅。
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: 4cc213db19a567f18c5ff483b64a75e3bbeef892
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962104"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>在订阅者与 CSP 之间转移 Azure 订阅

本文提供了用于在云解决方案提供商 (CSP) 合作伙伴与其客户之间转移 Azure 订阅的概要步骤。 此处的信息面向 Azure 订阅者，目的是帮助他们与其合作伙伴进行协调。 要了解 Microsoft 合作伙伴对转移过程的使用，可查看[了解如何将客户的 Azure 订阅转移到其他合作伙伴](/partner-center/switch-azure-subscriptions-to-a-different-partner)。

在开始转移请求之前，应下载或导出所有要保留的成本和计费信息。 计费和使用情况信息不会随订阅一起转移。 若要详细了解如何导出成本管理数据，请参阅[创建和管理导出的数据](../costs/tutorial-export-acm-data.md)。 若要详细了解如何下载发票和使用情况数据，请参阅[下载或查看 Azure 帐单发票和每日使用数据](download-azure-invoice-daily-usage-date.md)。

任何现有预留都会在转移订阅后停止应用。 在转移订阅之前，请务必[取消所有预留并完成退款](../reservations/exchange-and-refund-azure-reservations.md)。

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>将 EA 订阅转移到 CSP 合作伙伴

认证为 [Azure 专家托管服务提供商 (MSP)](https://partner.microsoft.com/membership/azure-expert-msp)的 CSP 直接帐单合作伙伴可以请求为其具有直接企业协议 (EA) 的客户转移 Azure 订阅。 仅允许为已接受了 Microsoft 客户协议 (MCA) 并通过 CSP 计划购买了某个 Azure 计划的客户进行订阅转移。

当请求获得批准后，CSP 可以向其客户提供组合发票。 若要详细了解 CSP 转移订阅，请参阅[获取你的 MPA 帐户的 Azure 订阅的计费所有权](mpa-request-ownership.md)。

>[!IMPORTANT]
> 将 EA 订阅转移到 CSP 合作伙伴后，以前应用到 EA 订阅的任何配额增加都将重置为默认值。 如果在订阅转移后需要额外的配额，请让 CSP 提供程序提交[配额增加](../../azure-portal/supportability/regional-quota-requests.md)请求。 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>将其他订阅转移到 CSP 合作伙伴

若要将任何其他 Azure 订阅转移到 CSP 合作伙伴，订阅者需要将资源从源订阅转移到 CSP 订阅。 请使用以下指南在订阅之间移动资源。

1. 与你的 CSP 合作伙伴合作来创建目标 Azure CSP 订阅。
1. 请确保源订阅和目标 CSP 订阅位于相同的 Azure Active Directory (Azure AD) 租户中。  
    你不能更改 Azure CSP 订阅的 Azure AD 租户。 相反，你必须将源订阅添加或关联到 CSP Azure AD 租户。 有关详细信息，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。
    > [!IMPORTANT]
    > - 将订阅关联到其他 Azure AD 目录时，如果用户的角色是使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) 分配的，则用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
    > - 将订阅与其他目录相关联时，还会从订阅中删除策略分配。
1. 用来执行转移的用户帐户必须对这两个订阅都具有 [Azure RBAC](add-change-subscription-administrator.md) 所有者访问权限。
1. 在开始之前，[验证](/rest/api/resources/resources/validatemoveresources)是否所有 Azure 资源都可以从源订阅移动到目标订阅。  
    某些 Azure 资源无法在订阅之间移动。 若要查看可移动的 Azure 资源的完整列表，请参阅[资源对移动操作的支持](../../azure-resource-manager/management/move-support-resources.md)。
    > [!IMPORTANT]
    >  - Azure CSP 仅支持 Azure 资源管理器资源。 如果源订阅中的 Azure 资源是使用 Azure 经典部署模型创建的，则在迁移之前必须先将其迁移到 [Azure 资源管理器](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm)。 你必须是合作伙伴才能查看此网页。

1. 验证是否所有源订阅服务都使用 Azure 资源管理器模型。 然后，使用 [Azure 资源移动](../../azure-resource-manager/management/move-resource-group-and-subscription.md)将资源从源订阅转移到目标订阅。
    > [!IMPORTANT]
    >  - 在订阅之间移动 Azure 资源可能会导致服务停机，这取决于订阅中的资源。

## <a name="transfer-csp-subscription-to-other-offer"></a>将 CSP 订阅转移到其他产品/服务

若要将任何其他订阅从 CSP 合作伙伴转移到任何其他 Azure 套餐，订阅者需要在源 CSP 订阅与目标订阅之间移动资源。

1. 创建目标 Azure 订阅。
1. 请确保源订阅和目标订阅位于相同的 Azure Active Directory (Azure AD) 租户中。 有关更改 Azure AD 租户的详细信息，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。
    请注意，CSP 订阅不支持更改目录选项。 例如，你要从云解决方案提供商订阅转移到即用即付订阅。 你需要更改即用即付订阅的目录，使之与该目录匹配。

    > [!IMPORTANT]
    >  - 将订阅关联到其他目录时，如果用户的角色是使用 [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) 分配的，则用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
    >  - 将订阅与其他目录相关联时，还会从订阅中删除策略分配。

1. 用来执行转移的用户帐户必须对这两个订阅都具有 [Azure RBAC](add-change-subscription-administrator.md) 所有者访问权限。
1. 在开始之前，[验证](/rest/api/resources/resources/validatemoveresources)是否所有 Azure 资源都可以从源订阅移动到目标订阅。
    > [!IMPORTANT]
    >  - 某些 Azure 资源无法在订阅之间移动。 若要查看可移动的 Azure 资源的完整列表，请参阅[资源对移动操作的支持](../../azure-resource-manager/management/move-support-resources.md)。

1. 使用 [Azure 资源移动](../../azure-resource-manager/management/move-resource-group-and-subscription.md)将资源从源订阅转移到目标订阅。
    > [!IMPORTANT]
    >  - 在订阅之间移动 Azure 资源可能会导致服务停机，这取决于订阅中的资源。

## <a name="next-steps"></a>后续步骤
- [获取 MPA 帐户的 Azure 订阅的计费所有权](mpa-request-ownership.md)。
- 了解如何[使用 Azure 计费管理帐户和订阅](../index.yml)。
