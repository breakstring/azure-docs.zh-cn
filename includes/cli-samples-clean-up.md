---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/21/2018
ms.author: cephalin
ms.openlocfilehash: e7494db94c7d8f0fc610ab297798749bffd55e7c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997888"
---
## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请在 Cloud Shell 中运行以下命令删除资源组：

```azurecli-interactive
az group delete --name myResourceGroup
```

此命令可能需要花费一点时间运行。