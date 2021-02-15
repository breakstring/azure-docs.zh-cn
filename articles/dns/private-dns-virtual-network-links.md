---
title: 什么是 Azure DNS 专用区域的虚拟网络链接子资源
description: Azure DNS 专用区域虚拟网络链接子资源概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954335"
---
# <a name="what-is-a-virtual-network-link"></a>什么是虚拟网络链接？

在 Azure 中创建专用 DNS 区域后，无法立即从任何虚拟网络对其进行访问。 必须先将其链接到虚拟网络，然后该网络中托管的 VM 才能访问专用 DNS 区域。
若要将专用 DNS 区域与虚拟网络链接，则必须在专用 DNS 区域下创建虚拟网络链接。 每个专用 DNS 区域都有一组虚拟网络链接子资源。 这些资源中的每个资源都代表与虚拟网络的连接。

可将虚拟网络作为注册虚拟网络或解析虚拟网络链接到专用 DNS 区域。

## <a name="registration-virtual-network"></a>注册虚拟网络

当你在专用 DNS 区域和虚拟网络之间[创建链接](./private-dns-getstarted-portal.md#link-the-virtual-network)时，可以选择为虚拟机打开 DNS 记录[自动注册](./private-dns-autoregistration.md)。 如果选择此选项，虚拟网络会成为专用 DNS 区域的注册虚拟网络。 将为在网络中部署的虚拟机自动创建 DNS 记录。 将为已在虚拟网络中部署的虚拟机创建 DNS 记录。 从虚拟网络的角度来看，专用 DNS 区域成为该虚拟网络的注册区域。
一个专用 DNS 区域可以具有多个注册虚拟网络，但每个虚拟网络只能有一个与其关联的注册区域。

## <a name="resolution-virtual-network"></a>解析虚拟网络

当你在专用 DNS 区域下创建虚拟网络链接并选择不启用 DNS 记录自动注册时，该虚拟网络将被视为仅限解析的虚拟网络。 在此类网络中部署的虚拟机的 DNS 记录不会在链接的专用 DNS 区域中自动创建。 但是，在此类网络中部署的虚拟机可以成功地从专用 DNS 区域查询 DNS 记录。 这些记录可由你手动创建，也可以从其他已作为注册网络与专用 DNS 区域链接的虚拟网络中进行填充。
一个专用 DNS 区域可以具有多个解析虚拟网络，且一个虚拟网络可以具有与其关联的多个解析区域。

## <a name="limits"></a>限制

若要了解注册网络和解析网络的数量，可以链接到专用 DNS 区域，请参阅 [Azure DNS 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="other-considerations"></a>其他注意事项

* 不支持使用经典部署模型部署的虚拟网络。

* 仅可在专用 DNS 区域和虚拟网络之间创建一个链接。

* 专用 DNS 区域下的每个虚拟网络链接必须在专用 DNS 区域上下文中具有唯一的名称。 可在不同的专用 DNS 区域中拥有具有相同名称的链接。

* 创建虚拟网络链接后，请检查虚拟网络链接资源的“链接状态”字段。 根据虚拟网络的大小，链接可能需要几分钟才能运行，并且“链接状态”会更改为“已完成”  。

* 删除虚拟网络时，所有虚拟网络链接和与之关联的其他专用 DNS 区域中自动注册的 DNS 记录都将自动删除。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure 门户](./private-dns-getstarted-portal.md#link-the-virtual-network)将虚拟网络链接到专用 DNS 区域

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。