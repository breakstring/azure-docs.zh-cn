---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021234"
---
## <a name="get-the-training-and-prediction-keys"></a>获取定型密钥和预测密钥

项目需要一组有效的订阅密钥才能与服务交互。 你可以在[自定义视觉网站](https://customvision.ai)查找这些密钥。 使用与用于创建自定义视觉资源的 Azure 帐户关联的帐户登录。 在主页（该页包含用于添加新项目的选项）上，选择右上角的 __齿轮图标__。 在列表中找到你的训练和预测资源并将其展开。 在此处可以找到训练密钥、预测密钥和预测资源 ID 值。 将这些值保存到临时位置。

> [!NOTE]
> 如果要使用认知服务一体化密钥来访问自定义视觉，你只会在设置屏幕上看到一个密钥。 在这种情况下，你会将相同的密钥用于训练和预测操作。

![密钥 UI 的图像](../media/csharp-tutorial/training-prediction-keys.png)

或者，可以在 [Azure 门户](https://www.portal.azure.com)中查看“自定义视觉训练和预测”资源并导航到“密钥”选项卡，来获取这些密钥和 ID。在此处可以找到训练密钥和预测密钥。 导航到“预测”资源的“属性”选项卡，获取预测资源 ID。

