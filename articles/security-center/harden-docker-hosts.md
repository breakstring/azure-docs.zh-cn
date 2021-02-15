---
title: 使用 Azure 安全中心强化 Docker 主机并保护容器
description: 如何保护 Docker 主机，并验证它们是否符合 CIS Docker 基准
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916228"
---
# <a name="harden-your-docker-hosts"></a>强化 Docker 主机

Azure 安全中心会识别在 IaaS Linux VM 上或其他运行 Docker 容器的 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 [Internet 安全中心 (CIS) 的 Docker 基准](https://www.cisecurity.org/benchmark/docker/)进行比较。

安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 在发现错误配置时，安全中心会生成安全建议。 使用安全中心的建议页面来查看建议和修正问题。

发现漏洞时，它们会被分组到一个建议中。

>[!NOTE]
> 这些 CIS 基准检查不会在 AKS 托管实例或 Databricks 托管 VM 上运行。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|所需角色和权限：|主机连接到的工作区上的读取器|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>识别和修正 Docker 配置中的安全漏洞

1. 在安全中心的菜单中，打开“建议”页。

1. 筛选到建议 应修正容器安全配置中的漏洞并选择建议。

    建议页显示受影响的资源（Docker 主机）。 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="修复容器安全配置中的漏洞的建议":::

1. 若要查看和修正特定的故障主机的 CIS 控件，请选择要调查的主机。 

    > [!TIP]
    > 如果是从“资产清单”页开始并在那里获得此建议，请选择“建议”页上的“执行操作”按钮。
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="用于启动 Log Analytics 的“执行操作”按钮":::

    Log Analytics 随即打开，其中包含可运行的自定义操作。 默认自定义查询包括评估的所有失败规则的列表，以及有助于你解决问题的指南。

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="带有查询的 Log Analytics 页，其中显示所有故障的 CIS 控件":::

1. 如有必要，请调整查询参数。

1. 当确定命令适合主机使用时，请选择“运行”。


## <a name="next-steps"></a>后续步骤

Docker 强化只是安全中心容器安全功能的一个方面。 

了解[安全中心的容器安全性](container-security.md)的详细信息。