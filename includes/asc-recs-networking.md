---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/09/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 4a303e5af2ce05102de6889d0bc5fce8af232a32
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007582"
---
这一类别有 15 条相关建议。

|建议 |说明 |严重性 |
|---|---|---|
|应限制对具有防火墙和虚拟网络配置的存储帐户的访问 |查看存储帐户防火墙设置中的网络访问设置。 建议配置网络规则，以便只有来自许可网络的应用程序才能访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。<br />（相关策略：[存储帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f34c877ad-507e-4c82-993e-3452a6e0ad3c)） |低 |
|应在面向内部的虚拟机上应用自适应网络强化建议 |Azure 安全中心已分析下面列出的虚拟机的 Internet 流量通信模式，并确定与它们关联的 NSG 中的现有规则过于宽松，导致潜在攻击面增加。 这可能是由于端口/协议元组或特定 IP 上的流量不足，这些 IP 已被安全中心的威胁情报源标记为恶意。<br />（无相关策略） |中型 |
|应在面向 Internet 的虚拟机上应用自适应网络强化建议 |Azure 安全中心已分析下面列出的虚拟机的 Internet 流量通信模式，并确定与它们关联的 NSG 中的现有规则过于宽容，导致潜在攻击面增加。<br>这通常在此 IP 地址不会定期与此资源通信的情况下发生。 或者，该 IP 地址已被安全中心的威胁情报源标记为恶意 IP。 <a href="https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening">详细了解</a><br />（相关策略：[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f08e6af2d-db70-460a-bfe9-d5bd474ba9d6)） |高 |
|应限制在与虚拟机关联的网络安全组上使用所有网络端口 |Azure 安全中心已识别到网络安全组的某些入站规则过于宽松。 入站规则不应允许从“任何”或“Internet”范围进行访问。 这有可能使得攻击者能够将你的资源定为攻击目标。<br />（相关策略：[应在与虚拟机关联的网络安全组上限制所有网络端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f9daedab3-fb2d-461e-b861-71790eead4f6)） |高 |
|应启用 Azure DDoS 防护标准 |安全中心发现了应用程序网关资源不受 DDoS 保护服务保护的虚拟网络。 这些资源包含公共 IP。 缓解网络容量和协议攻击。<br />（相关策略：[应启用 Azure DDoS 防护标准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa7aca53f-2ed4-4466-a25e-0b45ade68efd)） |中型 |
|面向 Internet 的虚拟机应使用网络安全组进行保护 |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则允许或拒绝来自同一子网内外的其他实例到 VM 的网络流量。<br>请注意，为了使计算机尽可能安全，必须限制 VM 对 Internet 的访问权限，并且应在子网上启用 NSG。<br>严重性为“高”的 VM 是面向 Internet 的 VM。<br />（相关策略：[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c)） |高 |
|应禁用虚拟机上的 IP 转发 |Azure 安全中心发现在某些虚拟机上已启用 IP 转发。 在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。<br />（相关策略：[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fbd352bd5-2853-4985-bf0d-73806b4a5744)） |中型 |
|应通过即时网络访问控制来保护虚拟机的管理端口 |Azure 安全中心已识别出一些对网络安全组中的管理端口过于宽松的入站规则。 启用实时访问控制，以保护 VM 免受基于 Internet 的暴力攻击。 <a href="https://docs.microsoft.com/azure/security-center/security-center-just-in-time">了解详细信息。</a><br />（相关策略：[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fb0f33259-77d7-4c9e-aac6-3aabcfae693c)） |高 |
|应关闭虚拟机上的管理端口 |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。<br />（相关策略：[应关闭虚拟机上的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f22730e10-96f6-4aac-ad84-9383d35b5917)） |中型 |
|应在 Linux 虚拟机上安装网络流量数据收集代理 |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。<br />（相关策略：[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f04c4380f-3fae-46e8-96c9-30193528f602)） |中型 |
|应在 Windows 虚拟机上安装网络流量数据收集代理 |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。<br />（相关策略：[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2f2ee1de-44aa-4762-b6bd-0893fc3f306d)） |中型 |
|应使用网络安全组来保护非面向 Internet 的虚拟机 |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范非面向 Internet 的 VM 遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则允许或拒绝从其他实例到 VM 的网络流量，无论它们是否位于同一子网中。<br>请注意，为了使计算机尽可能安全，必须限制 VM 对 Internet 的访问权限，并且应在子网上启用 NSG。<br />（相关策略：[应使用网络安全组来保护非面向 Internet 的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fbb91dfba-c30d-4263-9add-9c2384e659a6)） |低 |
|应该启用安全传输到存储帐户 |安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击。<br />（相关策略：[应启用到存储帐户的安全传输](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f404c3081-a854-4457-ae30-26a93ef643f9)） |高 |
|子网应与网络安全组关联 |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 当 NSG 与子网关联时，ACL 规则适用于该子网中的所有 VM 实例和集成服务，但不适用于子网内的内部流量。 若要确保同一子网中的资源彼此之间的安全，请直接在资源上启用 NSG。<br />（相关策略：[子网应与网络安全组关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fe71308d3-144b-4262-b144-efdc3cc90517)） |低 |
|虚拟网络应受 Azure 防火墙保护 |部分虚拟网络不受防火墙保护。 使用 Azure 防火墙限制虚拟网络的访问权限并防止潜在威胁。 若要详细了解 Azure 防火墙，请 <br> 单击<a href="https://azure.microsoft.com/pricing/details/azure-firewall">此处</a><br />（相关策略：[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ffc5e4038-4584-4632-8c85-c0448d374b2c)） |低 |
|||
