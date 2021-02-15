---
title: 不支持的语言部署-自定义转换器
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在 Azure 认知服务自定义转换器中部署不受支持的语言对。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898064"
---
# <a name="unsupported-language-deployments"></a>不支持的语言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

在即将停用 Microsoft Translator 中心后，Microsoft 将取消部署所有当前通过中心部署的模型。 其中许多模型都部署在其语言对不受自定义转换器支持的中心内。  我们不希望用户在这种情况下不会追索权翻译其内容。

现在，我们有了一个过程，可让你通过自定义转换器部署不受支持的模型。  此过程使你能够使用最新的 V3 API 继续转换内容。  这些模型将被承载，直到你选择取消部署它们，或者语言对在自定义转换器中变为可用。  本文介绍部署不受支持的语言对的模型的过程。

## <a name="prerequisites"></a>必备条件

为了使模型成为部署的候选项，它们必须满足以下条件：
* 必须已使用迁移工具将包含模型的项目从中心迁移到自定义转换器。  可在 [此处](how-to-migrate.md)找到迁移项目和工作区的进程。
* 迁移发生时，模型必须处于 "已部署" 状态。  
* 模型的语言对必须是自定义转换器中不支持的语言对。  一种语言对，其中一种语言支持英语，但它本身不包含英语，这是不受支持的语言部署的候选项。  例如，法语到德语语言对的集线器模型被视为不受支持的语言对，即使法语到英语和英语到德语也是受支持的语言对。

## <a name="process"></a>进程
在迁移适用于部署的中心的模型后，可以转到工作区的 " **设置** " 页，滚动到页面末尾，其中显示了 **不受支持的翻译人员中心培训** 部分。  仅当你的项目满足上述先决条件时才会显示此部分。

![突出显示了不受支持的翻译人员中心培训部分的屏幕截图。](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在 **不受支持的翻译中心培训** 选择页中，"未 **请求的培训** " 选项卡包含可用于部署的模型。  选择要部署的模型并提交请求。   在部署截止时间4月30日之前，你可以选择想要部署的任意数量的模型。
 
![显示 "未请求的培训" 选项卡的屏幕截图。](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交后，该模型将不再出现在 "未 **请求的培训** " 选项卡上，而会出现在 " **请求的培训** " 选项卡上。 你可以随时查看你请求的培训。

![如何从 Hub 迁移](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>下一步操作

在停止集线器并取消部署所有模型后，将保存选择用于部署的模型。  在5月24日前提交请求以部署不受支持的模型。  我们将于6月15日部署这些模型，此时可通过转换器 V3 API 访问这些模型。  此外，在7月1日前，它们将通过 V2 API 提供。  

若要深入了解在 "中心" 中弃用的重要日期，请在 [此处](https://www.microsoft.com/translator/business/hub/)查看。
部署完成后，将应用正常的托管费用。  有关详细信息，请参阅 [定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) 。  

与标准的自定义转换器模型不同，中心型号仅在单个区域中可用，因此，多区域托管费用将不适用。  部署完成后，你将能够通过迁移的自定义转换器项目随时取消部署中心模型。

## <a name="next-steps"></a>后续步骤

- [为模型定型](how-to-train-model.md)。
- 通过 [Microsoft 文本翻译 API V3](../reference/v3-0-translate.md?tabs=curl) 开始使用已部署的自定义翻译模型。