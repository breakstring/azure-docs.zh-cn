---
title: 部署概述 - Avere vFXT for Azure
description: 了解如何使用此概述为 Azure 群集部署 Avere vFXT。 相关文章具有特定的部署说明。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271220"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概述

本文概述启动并运行 Avere vFXT for Azure 群集所要执行的步骤。

在从 Azure 市场创建 vFXT 群集之前和之后，需要执行多个任务。 对自始至终的整个过程有个明确的认识将有助于规划所要完成的工作。

## <a name="deployment-steps"></a>部署步骤

[规划系统](avere-vfxt-deploy-plan.md)后，可以开始创建 Avere vFXT 群集。

Azure 市场中的 Azure 资源管理器模板会收集必需的信息并自动部署整个群集。

VFXT 群集启动并运行后，在使用之前仍需执行一些配置步骤。 如果创建了新的 Blob 存储容器，则需要将数据移动到其中。 如果使用 NAS 存储系统，则需要在创建群集后添加该存储系统。 需要将客户端连接到群集。

下面是所有步骤的概述。

1. 配置先决条件

   在创建 VM 之前，必须创建 Avere vFXT 项目的新订阅、配置订阅所有权、检查配额并根据需要请求提高配额，并接受 Avere vFXT 软件的使用条款。 有关详细说明，请参阅[准备创建 Avere vFXT](avere-vfxt-prereqs.md)。

1. 创建 Avere vFXT 群集

   使用 Azure 市场创建 Avere vFXT for Azure 群集。 模板将收集所需的信息并执行脚本来创建最终产品。

   群集创建涉及以下步骤，所有这些步骤都是通过市场模板执行的：

   * 根据需要创建新的网络基础结构和资源组
   * 创建群集控制器

     群集控制器是驻留在 Avere vFXT 群集所在的同一虚拟网络中的简单 VM，它具有创建和管理群集时所需的自定义软件。 该控制器可创建 vFXT 节点并组建群集，此外，还提供一个命令行接口，用于在群集的整个生存期内对其进行管理。

     如果在部署过程中创建新的虚拟网络或子网，控制器将具有公共 IP 地址。 这意味着，控制器可以充当用于从虚拟网络外部连接到群集的跳转主机。

   * 创建群集节点 Vm

   * 从各个节点创建群集

   * （可选）创建新的 Blob 容器，并将其配置为群集的后端存储

   [部署 vFXT 群集](avere-vfxt-deploy.md)中详细介绍了群集创建。

1. 配置群集

   连接到 Avere vFXT 配置接口（Avere 控制面板）以自定义群集的设置。 选择启用支持监视，如果使用硬件存储或其他 Blob 容器，则添加存储系统。

   * [访问 vFXT 群集](avere-vfxt-cluster-gui.md)
   * [启用支持](avere-vfxt-enable-support.md)
   * [配置存储](avere-vfxt-add-storage.md)（如果需要）

1. 装载客户端

   遵循[装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)中的指导原则来了解负载均衡，以及客户端计算机应如何装载群集。

1. 添加数据（如果需要）

   由于 Avere vFXT 是可伸缩的多客户端缓存，将数据移动到新的后端存储容器的最佳方式是使用多客户端多线程文件复制策略。

   如果需要将工作集数据移到新的 Blob 容器或其他后端存储系统，请按照将 [数据移动到 vFXT 群集](avere-vfxt-data-ingest.md)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

继续 [准备创建 Avere vFXT](avere-vfxt-prereqs.md) 以完成必备任务。
