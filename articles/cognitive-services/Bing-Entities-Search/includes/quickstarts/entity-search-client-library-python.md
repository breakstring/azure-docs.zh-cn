---
title: 必应实体搜索 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136597"
---
使用本快速入门开始通过适用于 Python 的必应实体搜索客户端库来搜索实体。 虽然必应实体搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* Python [2.x 或 3.x](https://www.python.org/)

* [适用于 Python 的必应实体搜索 SDK](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

建议使用 Python 虚拟环境。 可以使用 venv 模块安装并初始化虚拟环境。 可以使用以下命令安装 virtualenv：

```Console
python -m venv mytestenv
```

通过以下操作安装必应实体搜索客户端库：

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句。 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 为你的订阅密钥和终结点创建变量。 通过使用密钥创建新的 `CognitiveServicesCredentials` 对象，将客户端实例化。
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>发送搜索请求并接收响应

1. 使用 `client.entities.search()` 和一个搜索查询向必应实体搜索发送搜索请求。 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. 如果返回了实体，则将 `entity_data.entities.value` 转换为一个列表，并输出第一个结果。
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../../overview.md )