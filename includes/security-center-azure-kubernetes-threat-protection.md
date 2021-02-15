---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007800"
---
安全中心为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同的级别提供威胁防护： 

* **主机级别（由适用于服务器的 Azure Defender 提供）** - Azure Defender 利用安全中心在其他 VM 上使用的同一 Log Analytics 代理监视你的 Linux AKS 节点中是否存在可疑活动，例如 web shell 检测和与已知的可疑 IP 地址进行连接。 该代理还会监视特定于容器的分析，例如创建特权容器、以可疑方式访问 API 服务器以及在 Docker 容器内部运行安全外壳 (SSH) 服务器。

    如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

    >[!IMPORTANT]
    > 目前，我们不支持在虚拟机规模集上运行的 Azure Kubernetes 服务群集上安装 Log Analytics 代理。

    有关 AKS 主机级别的警报列表，请参阅[警报参考表](../articles/security-center/alerts-reference.md#alerts-containerhost)。


* **AKS 群集级别（由适用于 Kubernetes 的 Azure Defender 提供）** - 在群集级别，威胁防护基于对 Kubernetes 审核日志的分析。 要启用此无代理监视，请启用 Azure Defender。 为了在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    >[!NOTE]
    > 安全中心针对在订阅设置中启用“Kubernetes”选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

    有关 AKS 群集级别的警报列表，请参阅[警报参考表](../articles/security-center/alerts-reference.md#alerts-akscluster)。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。