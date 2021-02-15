---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109374"
---
若要在 Azure 门户中创建常规用途 v2 存储帐户，请执行以下步骤：

1. 在 Azure 门户菜单中，选择“所有服务”  。 在资源列表中，键入“存储帐户”  。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”  。
1. 在显示的“存储帐户”窗口中，选择“添加”。  
1. 在“基本信息”选项卡中，选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段中，选择所需的资源组或创建新的资源组。  有关 Azure 资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/management/overview.md)。
1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称的长度还必须介于 3 到 24 个字符之间，且只能包含数字和小写字母。
1. 选择存储帐户的位置或使用默认位置。
1. 选择性能层。 默认层为“标准”。
1. 将“帐户类型”字段设置为“存储 V2 (常规用途 v2)”。
1. 指定将如何复制存储帐户。 默认的复制选项为“读取访问权限异地冗余存储(RA-GRS)”。 要详细了解可用的复制选项，请参阅 [Azure 存储冗余](../articles/storage/common/storage-redundancy.md)。
1. “网络”、“数据保护”、“高级”和“标签”选项卡上提供了其他选项   。 若要使用 Azure Data Lake Storage，请选择“高级”选项卡，然后将“分层命名空间”设置为“启用”  。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../articles/storage/blobs/data-lake-storage-introduction.md)
1. 选择“查看+创建”可查看存储帐户设置并创建帐户。
1. 选择“创建” 。

下图显示了新存储帐户的“基本信息”选项卡上的设置：

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="显示如何在 Azure 门户中创建存储帐户的屏幕截图":::
