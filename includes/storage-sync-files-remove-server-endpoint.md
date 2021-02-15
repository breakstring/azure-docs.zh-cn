---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309942"
---
否：删除服务器终结点与重新启动服务器不一样！ 删除和重新创建服务器终结点几乎不是解决同步、云分层或 Azure 文件同步的其他方面问题的合适解决方案。删除服务器终结点是一个破坏性操作。 当分层文件存在于服务器终结点命名空间之外时，它可能会导致数据丢失。 有关详细信息，请参阅 [为什么在服务器终结点命名空间之外存在分层文件](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) 。 或者，它可能会导致服务器终结点命名空间中存在分层文件的无法访问的文件。 重新创建服务器终结点时，不会解决这些问题。 即使从未启用过云分层，分层文件也可能存在于服务器终结点命名空间中。 这就是为什么我们建议您不要删除服务器终结点，除非您想要停止将 Azure 文件同步用于此特定文件夹，或由 Microsoft 工程师明确指示您这样做。 有关删除服务器终结点的详细信息，请参阅[删除服务器终结点](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
