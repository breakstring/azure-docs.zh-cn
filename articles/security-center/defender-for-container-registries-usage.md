---
title: 如何对容器注册表使用 Azure Defender
description: 了解如何使用用于容器注册表的 Azure Defender 扫描 Linux 托管注册表中的 Linux 映像
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a5d66e43485ec66b6297ef11ed382e8fb82b7cb3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "96014562"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞

本页说明如何使用内置漏洞扫描程序扫描 Azure 资源管理器 Azure 容器注册表中存储的容器映像。

启用适用于容器注册表的 Azure Defender 时，会立即扫描推送到注册表的任何映像。 此外，还会扫描过去30天内提取的任何图像。 

当扫描程序向安全中心报告漏洞时，安全中心会将结果和相关信息显示为建议。 此外，这些发现还包括相关信息，如修正步骤、相关标识符、CVSS 评分等。 您可以查看一个或多个订阅或特定注册表的已识别漏洞。

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>标识 Azure 容器注册表映像中的漏洞 

若要对存储在 Azure 资源管理器 Azure 容器注册表中的映像启用漏洞扫描，请执行以下操作：

1. 为你的订阅启用 **容器注册表的 Azure Defender** 。 安全中心现已准备好扫描注册表中的映像。

    >[!NOTE]
    > 此功能按映像收费。

1. 每次推送或导入都会触发映像扫描，如果在过去的30天内请求映像，则为。 

    扫描完成后 (通常为大约2分钟后，但最多可以有15分钟) ，则可作为安全中心建议使用。

1. [查看并修正结果，如下所述](#view-and-remediate-findings)。

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>标识其他容器注册表中的映像中的漏洞 

1. 使用 ACR 工具将映像从 Docker 中心或 Microsoft 容器注册表引入到注册表。  导入完成后，Azure Defender 将扫描导入的映像。 

    在[将容器映像导入到容器注册表](../container-registry/container-registry-import-images.md)中了解详细信息

    扫描完成后 (通常为大约2分钟后，但最多可以有15分钟) ，则可作为安全中心建议使用。

1. [查看并修正结果，如下所述](#view-and-remediate-findings)。


## <a name="view-and-remediate-findings"></a>查看和修正发现

1. 若要查看发现结果，请访问“建议”页面。 如果发现问题，你将看到 **应修正 Azure 容器注册表映像中** 的建议漏洞

    ![解决问题的建议 ](media/monitor-container-security/acr-finding.png)

1. 选择建议。 

    “建议详细信息”页随即打开，并且其中包含其他信息。 这些信息包括具有易受攻击映像（“受影响的资源”）的注册表列表以及补救步骤。 

1. 选择特定的注册表以查看其中具有易受攻击存储库的存储库。

    ![选择一个注册表](media/monitor-container-security/acr-finding-select-registry.png)

    “注册表详细信息”页随即打开，并列出受影响的存储库。

1. 选择特定的存储库以查看其中具有易受攻击映像的存储库。

    ![选择存储库](media/monitor-container-security/acr-finding-select-repository.png)

    “存储库详细信息”页随即打开。 它列出了易受攻击的映像以及对发现结果严重性的评估。

1. 选择特定映像以查看漏洞。

    ![选择映像](media/monitor-container-security/acr-finding-select-image.png)

    所选映像的发现结果列表随即打开。

    ![发现结果列表](media/monitor-container-security/acr-findings.png)

1. 若要详细了解发现结果，请选择“发现结果”。 

    发现结果详细信息窗格随即打开。

    [![发现结果详细信息窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包括问题的详细说明，以及有助于缓解威胁的外部资源的链接。

1. 按照此窗格的“修正”部分中的步骤进行操作。

1. 执行修复安全问题所需的步骤后，请替换注册表中的映像：

    1. 推送已更新的映像。 这会触发扫描。 
    
    1. 请查看建议页面，了解“应修复 Azure 容器注册表映像中的漏洞”的建议。 
    
        如果建议仍然显示，并且你处理的映像仍显示在易受攻击映像列表中，请再次检查修正步骤。

    1. 确定更新的映像已推送、已扫描，且不再显示在建议中时，请从注册表中删除“旧”版本易受攻击的映像。


## <a name="disable-specific-findings-preview"></a> (预览禁用特定发现) 

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

当发现结果与在禁用规则中定义的条件相匹配时，它不会显示在发现结果列表中。 典型方案包括：

- 禁用严重性低于中型的发现结果
- 禁用非可修补的结果
- 禁用 CVSS 评分低于6.5 的发现结果
- 禁用安全检查或类别 (中包含特定文本的结果，例如 "RedHat"、"CentOS Security Update for sudo" ) 

> [!IMPORTANT]
> 若要创建规则，需要在 Azure 策略中编辑策略的权限。
>
> 有关详细信息，请参阅 azure [策略中的 AZURE RBAC 权限](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)。

您可以使用下列任一条件： 

- 查找 ID 
- 类别
- 安全检查 
- CVSS v3 评分
- 严重性 
- 可修补状态 

若要创建规则，请执行以下操作：

1. 对于 **Azure 容器注册表中的漏洞**，若要获取建议详细信息页，请选择 " **禁用规则**"。
1. 选择相关范围。
1. 定义条件。
1. 选择 " **应用规则**"。

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="为注册表上的 VA 发现创建禁用规则":::

1. 查看、替代或删除规则： 
    1. 选择 " **禁用规则**"。
    1. 在 "作用域" 列表中，具有活动规则的订阅显示为 "已 **应用规则**"。
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="修改或删除现有规则":::
    1. 若要查看或删除规则，请选择省略号菜单 ( "..." ) 。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Defender](azure-defender.md)