---
title: API 管理策略示例-添加包含相关 id 的标头
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何将包含相关 ID 的标头添加到入站请求。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 922565d26274aee12c9397c08c19330b4fce00e7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076294"
---
# <a name="add-a-header-containing-a-correlation-id"></a>添加包含相关 ID 的标头

本文介绍 Azure API 管理策略示例，该示例演示如何将包含相关 ID 的标头添加到入站请求。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-reference.md)