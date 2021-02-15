---
title: 部署存储同步服务
description: 部署 Azure 文件同步云资源，即存储同步服务。 跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043168"
---
在此步骤中，需要你的 Azure 订阅凭据。

要为 Azure 文件同步配置的核心资源称为 *存储同步服务* 。 建议只为当前或将来同步同一组文件的所有服务器部署一个服务器。 仅当你有不同的服务器集，而这些服务器必须永不交换数据时，才创建多个存储同步服务。 例如，你可能有一些服务器必须从不同步同一个 Azure 文件共享。 否则，单个存储同步服务是最佳实践。

选择与你所在位置接近的存储同步服务的 Azure 区域。 所有其他云资源必须部署在同一区域中。 若要简化管理，请在订阅中创建一个包含同步和存储资源的新资源组。

有关详细信息，请参阅关于部署 Azure 文件同步一文中 [有关部署存储同步服务的部分](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) 。请仅关注本文的这一部分。 稍后的步骤中将提供文章的其他部分的链接。