---
title: 网络概述-Azure Database for MySQL 灵活的服务器
description: 了解 Azure Database for MySQL 的灵活服务器部署选项中的连接和网络选项
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: a8e2d77ff3c7cb2e4352b21cd87d630331e28660
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906142"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版的连接和网络概念) 

本文介绍服务器的公共和专用连接选项。 你将详细了解 Azure Database for MySQL 灵活的服务器在 Azure 中安全地创建服务器的网络概念。

> [!IMPORTANT]
> Azure Database for MySQL-灵活的服务器处于预览阶段。

## <a name="choosing-a-networking-option"></a>选择网络选项
Azure Database for MySQL 灵活的服务器有两个网络选项。 这两个选项是“专用访问(VNet 集成)”和“公共访问(允许的 IP 地址)” 。 在创建服务器时，必须选择一个选项。 

> [!NOTE]
> 创建服务器后，不能更改网络选项。 

* 专用访问(VNet 集成) - 可以将灵活服务器部署到 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 虚拟网络中的各个资源可通过专用 IP 地址进行通信。

   如果需要以下功能，请选择“VNet 集成”选项：
   * 使用专用 IP 地址从同一虚拟网络中的 Azure 资源连接到灵活服务器
   * 使用 VPN 或 ExpressRoute 从非 Azure 资源连接到灵活服务器
   * 灵活的服务器没有公共终结点

* **公共访问 (允许的 IP 地址)** –通过公共终结点访问你的灵活服务器。 公共终结点是可公开解析的 DNS 地址。 “允许的 IP 地址”一词是指你选择向其授予访问服务器的权限的一系列 IP。 这些权限称为“防火墙规则”。 

   如果需要以下功能，请选择公共访问方法：
   * 从不支持虚拟网络的 Azure 资源进行连接
   * 从未通过 VPN 或 ExpressRoute 连接的 Azure 之外的资源进行连接 
   * 灵活的服务器具有一个公共终结点

无论你选择使用 "专用访问" 还是 "公共访问" 选项，都适用以下特征：
* 来自允许的 IP 地址的连接需要使用有效凭据向 MySQL 服务器进行身份验证
* [连接加密](#tls-and-ssl) 适用于你的网络流量
* 服务器 (fqdn) 具有完全限定的域名。 对于连接字符串中的 hostname 属性，我们建议使用 fqdn 而不是 IP 地址。
* 这两个选项都控制服务器级别的访问权限，而不是在数据库级或表级中。 你将使用 MySQL 的角色属性来控制数据库、表和其他对象访问权限。


## <a name="private-access-vnet-integration"></a>专用访问（VNet 集成）
使用虚拟网络 (vnet 进行专用访问) 集成为 MySQL 灵活的服务器提供私有和安全通信。

### <a name="virtual-network-concepts"></a>虚拟网络概念
下面是在将虚拟网络与 MySQL 灵活服务器一起使用时要熟悉的一些概念。

* **虚拟网络** -Azure 虚拟网络 (VNet) 包含配置为供你使用的专用 IP 地址空间。 若要详细了解 Azure 虚拟网络，请访问 [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md) 。

    你的虚拟网络必须与你的灵活服务器位于同一 Azure 区域。


* **委托子网** -虚拟网络包含子网 (子网络) 。 子网使你能够将虚拟网络分割为较小的地址空间。 将 Azure 资源部署到虚拟网络中的特定子网。 

   MySQL 灵活的服务器必须位于为 **仅用于 MySQL** 灵活服务器的子网中。 该委派意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。 可以通过将子网分配为 DBforMySQL/flexibleServers，来委托子网。

* **(NSG 的网络安全组)** 网络安全组中的安全规则使你可以筛选可以流入和流出虚拟网络子网和网络接口的网络流量类型。 有关详细信息，请参阅 [网络安全组概述](../../virtual-network/network-security-groups-overview.md) 。


### <a name="unsupported-virtual-network-scenarios"></a>不受支持的虚拟网络方案
* 公共终结点 (或公共 IP 或 DNS) -部署到虚拟网络的灵活服务器不能有公共终结点
* 将灵活的服务器部署到虚拟网络和子网后，不能将其移动到另一个虚拟网络或子网。 不能将虚拟网络移到另一个资源组或订阅中。
* 如果子网中存在资源，则不能增加 (地址空间) 的子网大小
* 不支持跨区域的对等互连 Vnet

了解如何使用 [Azure 门户](how-to-manage-virtual-network-portal.md) 或 [Azure CLI](how-to-manage-virtual-network-cli.md) (vnet 集成) 启用专用访问。

> [!NOTE]
> 如果使用的是自定义 DNS 服务器，则必须使用 DNS 转发器解析 Azure Database for MySQL 灵活服务器的 FQDN。 请参阅 [使用自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 以了解详细信息。

## <a name="public-access-allowed-ip-addresses"></a>公共访问（允许的 IP 地址）
公共访问方法的特征包括：
* 只有允许的 IP 地址具有访问 MySQL 灵活服务器的权限。 默认情况下，不允许任何 IP 地址。 可以在服务器创建过程中或之后添加 IP 地址。
* MySQL 服务器具有可公开解析的 DNS 名称
* 你的灵活服务器不在 Azure 虚拟网络中的一个
* 进出服务器的网络流量不会通过专用网络。 流量使用一般的 internet 路径。

### <a name="firewall-rules"></a>防火墙规则
向 IP 地址授予权限称为防火墙规则。 如果连接尝试来自不允许使用的 IP 地址，则原始客户端将会看到错误。


### <a name="allowing-all-azure-ip-addresses"></a>允许所有 Azure IP 地址
如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接。

> [!IMPORTANT]
> **允许 azure 中的 azure 服务和资源的公共访问** 选项将防火墙配置为允许来自 azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。

了解如何使用 [Azure 门户](how-to-manage-firewall-portal.md) 或 [Azure CLI](how-to-manage-firewall-cli.md) (允许的 IP) 地址启用和管理公共访问权限。


### <a name="troubleshooting-public-access-issues"></a>排查公共访问问题
当访问 Microsoft Azure MySQL Server 服务的数据库不按预期方式工作时，请注意以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for MySQL 服务器防火墙配置所做的更改可能需要多达 5 分钟的延迟才可生效。

* **身份验证失败：** 如果用户对 Azure Database for MySQL 服务器没有权限或者使用的密码不正确，则拒绝连接到 Azure Database for MySQL 服务器。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会。 每个客户端仍必须提供必需的安全凭据。

* **动态客户端 IP 地址：** 如果你的 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

   * 向 Internet 服务提供商 (ISP) 分配给访问 Azure Database for MySQL 服务器的客户端计算机的 IP 地址范围，然后将该 IP 地址范围添加为防火墙规则。
   * 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。
  
* **防火墙规则不适用于 IPv6 格式：** 防火墙规则必须为 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。


## <a name="hostname"></a>主机名
无论你选择哪种网络选项，我们都建议你始终使用完全限定的域名 (FQDN) 在连接到灵活的服务器时使用主机名。 不保证服务器的 IP 地址保持静态。 使用 FQDN 有助于避免更改连接字符串。 

示例
* 您 `hostname = servername.mysql.database.azure.com`
* 如果可能，请避免使用 `hostname = 10.0.0.4`) 或 `hostname = 40.2.45.67` (公共 IP (专用地址) 



## <a name="tls-and-ssl"></a>TLS 和 SSL
Azure Database for MySQL 灵活的服务器支持使用传输层安全性 (TLS) 将客户端应用程序连接到 MySQL 服务。 TLS 是一种行业标准协议，可确保数据库服务器和客户端应用程序之间的加密网络连接。 TLS 是安全套接字层 (SSL) 的更新协议。

Azure Database for MySQL 灵活的服务器仅支持使用传输层安全性 (TLS 1.2) 进行加密的连接。 将拒绝所有 TLS 为1.0 和 TLS 1.1 的传入连接。 不能禁用或更改用于连接到 Azure Database for MySQL 灵活服务器的 TLS 版本。


## <a name="next-steps"></a>后续步骤
* 了解如何使用 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 启用专用访问（VNet 集成）
* 了解如何使用 [Azure 门户](how-to-manage-firewall-portal.md)或 [Azure CLI](how-to-manage-firewall-cli.md) 启用公共访问（允许的 IP 地址）
* 了解如何 [使用 TLS](how-to-connect-tls-ssl.md)
