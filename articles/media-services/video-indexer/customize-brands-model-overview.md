---
title: 在视频索引器中自定义品牌模型 - Azure
titleSuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的品牌模型，以及如何自定义它。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586068"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>在视频索引器中自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用视频索引器从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果在某个视频或音频内容中提到了 Microsoft，或者 Microsoft 出现在视频的视觉文本中，则视频索引器会将其作为内容中的品牌检测到。 将使用上下文将品牌与其他术语进行区分。

品牌检测在许多业务场景中非常有用，例如，内容存档和发现、上下文广告、社交媒体分析、零售竞争分析，等等。 通过视频索引器品牌检测，可以使用必应的品牌数据库以及自定义项通过为每个视频索引器帐户构建一个自定义品牌模型，为在语音和视觉文本中提到的品牌编制索引。 使用 "自定义品牌模型" 功能，您可以选择是否使用视频索引器来检测来自必应品牌数据库的品牌，排除某些品牌 (实质上是创建未批准) 品牌的列表，并包含可能不属于必应品牌数据库的型号的品牌 (实质上是创建已批准品牌的列表) 。 所创建的自定义品牌模型仅在创建该模型的帐户中可用。

## <a name="out-of-the-box-detection-example"></a>现成的检测示例

在 "Microsoft Build 2017 Day 2" 演示中，品牌 "Microsoft Windows" 出现多次。 有时是在脚本中，有时是作为视觉文本，但从未以表示字面含义的形式出现。 视频索引器根据上下文以高精度检测某个术语是否确实为品牌，它涵盖了 9 万多个现成的品牌并且持续在更新。 在 02:25，视频索引器从语音中检测到品牌，然后在 02:40 再次从视觉文本中检测到品牌，该品牌是 Windows 徽标的一部分。

![品牌概述](./media/content-model-customization/brands-overview.png)

高级机器学习算法知道如何根据上下文消除歧义，在建筑上下文中谈论 windows（窗户）时，不会将单词“Windows”检测为品牌，对于 Box、Apple 和 Fox 等也是如此。 品牌检测可以针对我们支持的所有语言进行工作。  

## <a name="next-steps"></a>后续步骤

若要自带品牌，请查看以下主题：

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)

[使用网站自定义品牌模型](customize-brands-model-with-website.md)
