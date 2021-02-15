---
title: 快速入门：使用 Python 和必应新闻搜索 REST API 执行新闻搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Python 将请求发送到必应新闻搜索 REST API，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: f47c0c5ad271b89348fe0baa8ac1fd5ebd9cf2eb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351259"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>快速入门：使用 Python 和必应新闻搜索 REST API 执行新闻搜索

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应资讯搜索 API 进行第一次调用。 这个简单的 askSpoke 应用程序会向 API 发送一个搜索查询并处理 JSON 结果。 

虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

若要在 [MyBinder](https://mybinder.org) 上将此代码示例作为 Jupyter 笔记本运行，请选择“启动活页夹”锁屏提醒： 

[![启动活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

该示例的源代码还可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py) 上获得。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

在你最喜欢使用的 IDE 或编辑器中新建一个 Python 文件，并导入请求模块。 为订阅密钥、终结点和搜索词创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>为请求创建参数

将你的订阅密钥添加到一个新字典中，使用 `Ocp-Apim-Subscription-Key` 作为键。 为搜索参数执行相同的操作。

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>发送请求并获取响应

1. 使用请求库以及你的订阅密钥和在上一步骤中创建的字典对象调用必应视觉搜索 API。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. 访问 API 响应中包含的项目的说明，该 API 以 JSON 对象的形式存储在 `search_results` 中。 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>显示结果

然后，这些说明可以呈现为表格，其中搜索关键字用粗体突出显示。

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)