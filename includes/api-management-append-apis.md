---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183802"
---
## <a name="append-other-apis"></a>追加其他 API

API 可以包含由其他服务公开的 API，这些服务包括 OpenAPI 规范、SOAP API、Azure 应用服务的 API 应用功能、Azure Function App、Azure 逻辑应用和 Azure Service Fabric。

![导入 API](./media/api-management-append-apis/import.png)

若要将其他 API 追加到现有 API，请完成以下步骤。 导入另一个 API 时，操作将追加到当前 API 后面。

1. 在 Azure 门户中转到自己的 Azure API 管理实例。
2. 在左侧菜单中选择“API”  。
3. 单击要将另一个 API 追加到的 API 旁边的“...”  。
4. 从下拉菜单中选择“导入”。 
5. 选择要从中导入 API 的服务。