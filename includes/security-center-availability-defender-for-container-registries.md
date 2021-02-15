---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98187020"
---
## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|适用于容器注册表的 Azure Defender 按[定价页](../articles/security-center/security-center-pricing.md)中的定价计费|
|支持的注册表和映像：|可通过 shell 从公共 internet 访问的 ACR 注册表中的 Linux 映像|
|不支持的注册表和映像：|Windows 映像<br>“专用”注册表<br>通过防火墙、服务终结点或专用终结点（如 Azure 专用链接）限制了访问权限的注册表<br>超级简单的映像，例如 [Docker 暂存](https://hub.docker.com/_/scratch/)映像或仅包含应用程序及其运行时依赖项而无包管理器、shell 或 OS 的“无分发版”映像|
|所需角色和权限：|安全读取者和 [Azure 容器注册表角色和权限](../articles/container-registry/container-registry-roles.md)|
|云：|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 商用云<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov 和 China Gov - 当前仅支持“推送扫描”功能。 参阅[何时扫描映像？](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)了解详细信息|
|||