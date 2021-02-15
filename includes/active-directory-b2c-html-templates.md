---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990863"
---
## <a name="sample-templates"></a>示例模板
可以在以下位置找到用于 UI 自定义的示例模板：

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

此项目包含以下模板：
- [海蓝](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [青灰](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

使用示例：

1. 将存储库克隆到本地计算机。 选择模板文件夹 `/ocean_blue` 或 `/slate_gray` 。
1. 将模板文件夹和文件夹下的所有文件上传 `/assets` 到 Blob 存储，如前一部分中所述。
1. 接下来，打开 `\*.html` 或的根目录中的每个文件 `/ocean_blue` `/slate_gray` ，将相对 url 的所有实例替换为在步骤2中上载的 css、图像和字体文件的 url。 例如：
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    如果
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. 保存 `\*.html` 文件并将其上传到 Blob 存储。
1. 现在，如前文所述，修改策略，指向 HTML 文件。
1. 如果发现缺少字体、图像或 CSS，请检查扩展策略和 .html 文件中的引用 \* 。
