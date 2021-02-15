---
title: 在 Python 的 MSAL 中记录错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何在 MSAL for Python 中记录错误和异常
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063108"
---
# <a name="logging-in-msal-for-python"></a>MSAL for Python 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>适用于 Python 的 MSAL 日志记录

MSAL Python 中的日志记录使用标准的 Python 日志记录机制，例如 `logging.info("msg")`。可按如下所示配置 MSAL 日志记录（并在 [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) 中查看其运作方式）：

### <a name="enable-debug-logging-for-all-modules"></a>为所有模块启用调试日志记录

默认已禁用任何 Python 脚本中的日志记录。 若要对整个 Python 脚本中的所有模块启用调试日志记录，请使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>仅消隐 MSAL 日志记录

若要仅消隐 MSAL 库日志记录，同时在 Python 脚本的所有其他模块中启用调试日志记录，请禁用 MSAL Python 使用的记录器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的个人和组织数据

适用于 Python 的 MSAL 不会记录个人数据或组织数据。 没有任何属性可用于启用或者禁用个人或组织数据的日志记录。

你可以使用标准的 Python 日志记录来记录所需的任何内容，但需要责任安全处理敏感数据并遵守法规要求。

有关 Python 中的日志记录的详细信息，请参阅 Python 的  [日志记录：操作方法](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。
