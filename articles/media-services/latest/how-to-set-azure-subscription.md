---
title: 如何查找 Azure 订阅
description: 查找 Azure 订阅，以便设置环境。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: 07088ebfa5fde4c3c790bd554c1eba8d6844b7d1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736074"
---
# <a name="find-your-azure-subscription"></a>查找你的 Azure 订阅

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“Azure 服务”标题下，选择“订阅”。 （如果未列出任何订阅，可能需要切换 Azure AD 租户。）订阅 ID 在第二列中列出。
1. 复制订阅 ID 并将其粘贴到所选的文本文档中供稍后使用。

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>使用 CLI 列出 Azure 订阅

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>另请参阅

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)
