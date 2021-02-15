---
title: 快速入门：使用 Python 执行搜索 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Python 将请求发送到必应 Web 搜索 REST API，并接收 JSON 响应
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 5a9eff1ef012e66f661ee99852a8d82f117a45c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350511"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>快速入门：使用 Python 调用必应 Web 搜索 API  

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应 Web 搜索 API 进行第一次调用。 这个 Python 应用程序会向该 API 发送一个搜索请求并显示 JSON 响应。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

此示例作为 Jupyter 笔记本在 [MyBinder](https://mybinder.org) 上运行。 若要运行它，请选择“启动活页夹”锁屏提醒：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>先决条件

* [Python 2.x 或 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>定义变量

1. 将 `subscription_key` 值替换为来自你的 Azure 帐户的有效订阅密钥。

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. 声明必应 Web 搜索 API 终结点。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. （可选）通过替换 `search_term` 的值自定义搜索查询。

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>发出请求

此代码使用 `requests` 库调用必应 Web 搜索 API 并将结果作为 JSON 对象返回。 API 密钥是在 `headers` 字典中传入的，搜索词和查询参数是在 `params` 字典中传入的。 

有关选项和参数的完整列表，请参阅[必应 Web 搜索 API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)。

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>格式化并显示响应

`search_results` 对象包括搜索结果以及相关查询和页等元数据。 此代码使用 `IPython.display` 库格式化响应并在浏览器中显示响应。

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub 上的示例代码

若要在本地运行此代码，请参阅 [GitHub 上提供的完整的示例](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应 Web 搜索 API 单页应用教程](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]