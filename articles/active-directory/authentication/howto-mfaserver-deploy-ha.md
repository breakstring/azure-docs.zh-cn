---
title: Azure MFA 服务器的高可用性 - Azure Active Directory
description: 在配置中部署可提供高可用性的多个 Azure 多重身份验证服务器实例。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584470"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>配置 Azure 多重身份验证服务器以实现高可用性

若要使用 Azure 服务器 MFA 部署实现高可用性，需要部署多个 MFA 服务器。 本部分提供有关可在 Azure MFS 服务器部署中实现高可用性目标的负载均衡设计的信息。

> [!IMPORTANT]
> 从2019年7月1日起，Microsoft 不再为新部署提供 MFA 服务器。 希望在登录事件期间 (MFA) 需要多重身份验证的新客户应使用基于云的 Azure AD 多重身份验证。
>
> 若要开始执行基于云的 MFA，请参阅 [教程：通过 Azure AD 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)。
>
> 在2019年7月1日之前激活 MFA 服务器的现有客户，可以下载最新版本、将来的更新，并照常生成激活凭据。

## <a name="mfa-server-overview"></a>MFA 服务器概述

Azure MFA 服务器服务体系结构由下图中所示的多个组件构成：

 ![MFA 服务器体系结构组件](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA 服务器是装有 Azure 多重身份验证软件的 Windows 服务器。 MFA 服务器实例必须由 Azure 中的 MFA 服务激活才能正常运行。 可在本地安装多个 MFA 服务器。

默认情况下，默认情况下，安装的每个 MFA 服务器都是由 Azure MFA 服务激活时的主要 MFA 服务器。 主 MFA 服务器具有 PhoneFactor. phonefactor.pfdata 数据库的可写副本。 后续安装的 MFA 服务器实例称为从属服务器。 MFA 从属服务器具有复制的 PhoneFactor.pfdata 数据库只读副本。 MFA 服务器使用远程过程调用 (RPC) 复制信息。 所有 MFA 服务器必须统一加入域，或者各自保持独立，这样才能复制信息。

当需要双因素身份验证时，MFA 主要和从属 MFA 服务器都与 MFA 服务通信。 例如，当某个用户尝试获取对需要双重身份验证的应用程序的访问权限时，首先标识提供者（例如 Active Directory (AD)）会验证该用户的身份。

在 AD 中成功完成身份验证后，MFA 服务器会与 MFA 服务通信。 MFA 服务器等待 MFA 服务发出允许或拒绝用户访问应用程序的通知。

如果 MFA 主服务器脱机，则仍可处理身份验证，但不能处理需要更改 MFA 数据库的操作。 （示例包括：添加用户、自助 PIN 更改、更改用户信息或访问用户门户）

## <a name="deployment"></a>部署

请注意以下有关负载均衡 Azure MFA 服务器及其相关组件的要点。

* **使用 RADIUS 标准实现高可用性**。 如果使用 Azure MFA 服务器作为 RADIUS 服务器，有可能会将一台 MFA 服务器配置为主 RADIUS 身份验证目标，并将其他 Azure MFA 服务器配置为辅助身份验证目标。 但是，这种实现高可用性的方法有时不可行，因为在主身份验证目标上发生身份验证失败时，必须等待超时期限过去，然后才能针对辅助身份验证目标进行身份验证。 在 RADIUS 客户端与 RADIUS 服务器（在本例中，Azure MFA 服务器充当 RADIUS 服务器）之间负载均衡 RADIUS 流量的做法更有效，这样就可以使用 RADIUS 客户端指向的 URL 来配置这些客户端。
* **需要手动提升 MFA 从属服务器**。 如果主 Azure MFA 服务器脱机，则辅助 Azure MFA 服务器将继续处理 MFA 请求。 但是，在主 MFA 服务器可用之前，管理员无法添加用户或修改 MFA 设置，用户也不能使用用户门户进行更改。 升级到主要角色的 MFA 始终是一个手动过程。
* **组件的隔离性**。 Azure MFA 服务器包含多个可在相同或不同 Windows Server 实例上安装的组件。 这些组件包括用户门户、移动应用 Web 服务和 ADFS 适配器（代理）。 借助这种隔离性，可以使用 Web 应用程序代理从外围网络发布用户门户和移动应用 Web 服务器。 此类配置会添加到设计的整体安全性中，如下图所示。 也可以在 HA 负载均衡的配置中部署 MFA 用户门户和移动应用 Web 服务器。

   ![使用外围网络的 MFA 服务器](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **一次性密码 (OTP) 通过 SMS (也称为单向短信) 在流量负载平衡时需要使用粘滞会话**。 单向短信是导致 MFA 服务器向用户发送包含 OTP 的短信的身份验证选项。 用户需在提示窗口中输入 OTP 来完成 MFA 质询。 如果负载均衡 Azure MFA 服务器，为初始身份验证请求提供服务的同一台服务器必须是从用户接收 OTP 消息的服务器；如果另一台 MFA 服务器接收 OTP 回复，则身份验证质询会失败。 有关详细信息，请参阅[已在 Azure MFA 服务器中添加基于短信的一次性密码](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)。
* **用户门户和移动应用 Web 服务的负载均衡部署需要粘性会话**。 如果负载均衡 MFA 用户门户和移动应用 Web 服务，每个会话需要保留在同一台服务器上。

## <a name="high-availability-deployment"></a>高可用性部署

下图显示了 Azure MFA 及其组件的完整 HA 负载均衡实现，并显示了 ADFS 供参考。

 ![Azure MFA 服务器 HA 实现](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

请注意上图中带有相应编号的区域的以下各项。

1. 两台 Azure MFA 服务器（MFA1 和 MFA2）经过负载均衡 (mfaapp.contoso.com)，并已配置为使用静态端口 (4443) 来复制 PhoneFactor.pfdata 数据库。 Web 服务 SDK 安装在每台 MFA 服务器上，以便能够通过 TCP 端口 443 来与 ADFS 服务器通信。 MFA 服务器部署在无状态负载均衡配置中。 但是，若要使用基于短信的 OTP，必须使用有状态负载均衡。
   ![Azure MFA 服务器 - 应用服务器 HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > 由于 RPC 使用动态端口，我们不建议在防火墙中开放 RPC 可能使用的动态端口范围。 如果在 MFA 应用程序服务器 **之间** 有防火墙，则应该将 mfa 服务器配置为在静态端口上与从属服务器和主服务器之间的复制流量通信，并在防火墙上打开该端口。 可以通过在 ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` 中创建名为 ```Pfsvc_ncan_ip_tcp_port``` 的 DWORD 注册表值并将其设置为可用的静态端口，来强制使用静态端口。 连接始终由从属 MFA 服务器启动到主服务器，只需在主服务器上使用静态端口，但由于可以随时将从属服务器提升为主服务器，因此应在所有 MFA 服务器上设置静态端口。

2. 两台用户门户/MFA 移动应用服务器（MFA-UP-MAS1 和 MFA-UP-MAS2）已在 **有状态** 配置 (mfa.contoso.com) 中经过负载均衡。 请记住，粘性会话是负载均衡 MFA 用户门户和移动应用服务的一项要求。
   ![Azure MFA 服务器 - 用户门户和移动应用服务 HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. ADFS 服务器场经过负载均衡，通过外围网络中负载均衡的 ADFS 代理发布到 Internet。 每台 ADFS 服务器借助 ADFS 代理，使用负载均衡的单个 URL (mfaapp.contoso.com) 通过 TCP 端口 443 来与 Azure MFA 服务器通信。

## <a name="next-steps"></a>后续步骤

* [安装和配置 Azure MFA 服务器](howto-mfaserver-deploy.md)
