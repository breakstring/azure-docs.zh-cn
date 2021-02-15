---
title: Windows 虚拟桌面必需的 URL 列表-Azure
description: 必须取消阻止的 Url 的列表，以确保 Windows 虚拟桌面部署按预期工作。
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251754"
---
# <a name="required-url-list"></a>所需的 URL 列表

若要部署和使用 Windows 虚拟桌面，必须取消阻止某些 Url，以便 (Vm) 的虚拟机可以随时访问。 本文列出了需要取消阻止 Windows 虚拟桌面才能正常运行所需的 Url。 

>[!IMPORTANT]
>Windows 虚拟桌面不支持阻止本文中列出的 Url 的部署。

## <a name="virtual-machines"></a>虚拟机

你为 Windows 虚拟桌面创建的 Azure 虚拟机必须能够访问 Azure 商业云中的以下 Url：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|代理流量|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|代理流量|AzureCloud|
|* xt.blob.core.windows.net|443|代理流量|AzureCloud|
|* eh.servicebus.windows.net|443|代理流量|AzureCloud|
|* xt.table.core.windows.net|443|代理流量|AzureCloud|
|* xt.queue.core.windows.net|443|代理流量|AzureCloud|
|catalogartifact.azureedge.net|443|Azure 市场|AzureCloud|
|kms.core.windows.net|1688|Windows 激活|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|代理和 SXS 堆栈更新|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure 门户支持|AzureCloud|
| 169.254.169.254 | 80 | [Azure 实例元数据服务终结点](../virtual-machines/windows/instance-metadata-service.md) | 空值 |
| 168.63.129.16 | 80 | [会话主机运行状况监视](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 空值 |

>[!IMPORTANT]
>Windows 虚拟桌面现在支持 FQDN 标记。 有关详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](../firewall/protect-windows-virtual-desktop.md)。
>
>建议使用 FQDN 标记或服务标记（而不是 URL）来防止服务问题。 列出的 URL 和标记只对应于 Windows 虚拟桌面站点和资源。 这些 URL 不包含其他服务（如 Azure Active Directory）的 URL。

你为 Windows 虚拟桌面创建的 Azure 虚拟机必须能够访问 Azure 政府云中的以下 Url：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*. wvd.microsoft.us|443|服务流量|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|代理流量|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|代理流量|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows 激活|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|代理和 SXS 堆栈更新|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure 门户支持|AzureCloud|
| 169.254.169.254 | 80 | [Azure 实例元数据服务终结点](../virtual-machines/windows/instance-metadata-service.md) | 空值 |
| 168.63.129.16 | 80 | [会话主机运行状况监视](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 空值 |

下表列出了 Azure 虚拟机可以访问的可选 URL：

|地址|出站 TCP 端口|目的|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|向 Microsoft Online Services 进行身份验证|login.microsoftonline.us|
|*.events.data.microsoft.com|443|遥测服务|无|
|www.msftconnecttest.com|443|检测 OS 是否已连接到 Internet|无|
|*.prod.do.dsp.mp.microsoft.com|443|Windows 更新|无|
|login.windows.net|443|登录到 Microsoft 365 等 Microsoft Online Services|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive 客户端软件更新|oneclient.sfx.ms|
|*.digicert.com|443|证书吊销检查|无|
|*. azure-dns.com|443|Azure DNS 分辨率|无|
|*. azure-dns.net|443|Azure DNS 分辨率|无|

>[!NOTE]
>Windows 虚拟桌面当前没有 IP 地址范围的列表，你可以取消阻止这些 IP 地址范围以允许网络流量。 目前仅支持取消阻止特定的 Url。
>
>如果使用下一代防火墙 (NGFW) ，则需要使用专门为 Azure Ip 创建的动态列表，以确保可以进行连接。
>
>有关与安全 Office 相关的 Url （包括所需 Azure Active Directory 相关 Url）的列表，请参阅 [Office 365 url 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges)。
>
>对于涉及服务流量的 URL，必须使用通配符 (*)。 如果不希望为代理相关的流量使用 *，下面提供了有关如何在不使用通配符的情况下查找 URL 的信息：
>
>1. 向 Windows 虚拟桌面主机池注册你的虚拟机。
>2. 打开 **事件查看器**，然后切换到 **Windows 日志**  >  **应用程序**  >  **WVD** ，并查找事件 ID 3701。
>3. 取消阻止在事件 ID 3701 下找到的 Url。 事件 ID 3701 下的 Url 是特定于区域的。 需要为要在其中部署虚拟机的每个区域的相关 Url 重复取消阻止进程。

## <a name="remote-desktop-clients"></a>远程桌面客户端

你使用的任何远程桌面客户端都必须具有以下 Url 的访问权限：

|地址|出站 TCP 端口|目的|客户端|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|All|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|排查数据问题|All|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLink|All|无|
|aka.ms|443|Microsoft URL 缩短符|All|无|
|docs.microsoft.com|443|文档|All|无|
|privacy.microsoft.com|443|隐私声明|All|无|
|query.prod.cms.rt.microsoft.com|443|客户端更新|Windows 桌面|无|

>[!IMPORTANT]
>若要获取可靠的客户端体验，必须打开这些 URL。 不支持阻止访问这些 URL，否则会影响服务功能。
>
>这些 Url 仅对应于客户端站点和资源。 此列表不包含其他服务（如 Azure Active Directory）的 Url。 在 [Office 365 url 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)的 ID 56 下可以找到 Azure Active Directory url。