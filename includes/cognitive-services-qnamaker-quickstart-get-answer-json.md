---
title: include 文件
description: include 文件
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 12/29/2020
ms.author: mrbullwin
ms.openlocfilehash: 8269cd3905ec7ea3ad0ff2ab6bfa31cc95da5b4f
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820600"
---
## <a name="request-and-response-json"></a>请求和响应 JSON

针对 REST API 的 JSON 格式问题示例：

```json
{
    "question": "Is the QnA Maker Service free?",
    "top": 3
}
```

此问题包含一个可返回前三个答案的属性。 

答案在 JSON 对象中返回：

```json
{
  "answers": [
    {
      "questions": [
        "How do I embed the QnA Maker service in my website?"
      ],
      "answer": "Follow these steps to embed the QnA Maker service as a web-chat control in your website:\n\n\n1.  Create your FAQ bot by following the instructions [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot).\n2.  Enable the web chat by following the steps [here](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)",
      "score": 70.95,
      "id": 16,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "Do I need to use Bot Framework in order to use QnA Maker?"
      ],
      "answer": "No, you do not need to use the Bot Framework with QnA Maker. However, QnA Maker is offered as one of several templates in Azure Bot Service. Bot Service enables rapid intelligent bot development through Microsoft Bot Framework, and it runs in a server-less environment.",
      "score": 46.94,
      "id": 14,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "How can I create a bot with QnA Maker?"
      ],
      "answer": "Follow the instructions in [this](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot)documentation to create your Bot with Azure Bot Service.",
      "score": 43.25,
      "id": 15,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```
