---
title: 在 LUIS 门户中测试应用
description: 使用语言理解 (LUIS) 持续优化应用程序并改进其语言理解能力。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790912"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>在 LUIS 门户中测试 LUIS 应用

对应用进行[测试](luis-concept-test.md)是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看应用是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新和训练，然后再次测试。

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>测试前训练

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 为针对最新版的活动应用进行测试，请在测试之前从顶部菜单中选择“训练”。

## <a name="test-an-utterance"></a>测试陈述

测试言语不应与应用中的任何示例言语完全相同。 测试言语应包括预期用户使用的选词、短语长度和实体用法。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。

1. 若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试” 。

    > [!div class="mx-imgBorder"]
    > ![训练和测试应用页](./media/luis-how-to-interactive-test/test.png)

1. 在文本框中输入陈述，然后按 Enter。 虽然在“测试”中可键入任意数量的测试陈述，但一次只能键入一个。

1. 陈述的最高意向和分数会添加至文本框下方的陈述列表。

    > [!div class="mx-imgBorder"]
    > ![交互式测试识别错误意向](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>检查预测

在“检查”面板中检查测试结果的详细信息。

1. 打开“测试”滑出面板后，对想要比对的陈述选择“检查” 。

    > [!div class="mx-imgBorder"]
    > ![选择“检查”按钮可查看有关测试结果的更多详细信息](./media/luis-how-to-interactive-test/inspect.png)

1. 此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选言语的预测。

    > [!div class="mx-imgBorder"]
    > ![“测试检查”面板的部分屏幕截图](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>添加到示例言语

从检查面板中，可以通过选择“添加到示例言语”将测试言语添加到意图。

## <a name="disable-required-features"></a>禁用所需的功能

此切换按钮可帮助你确定经过训练的应用是否根据所需功能正确预测你的实体。 默认设置是在预测过程中按需应用该功能。 如果子实体的功能不是必需的，则选择此开关可以查看预测的情况。

### <a name="when-to-disable-required-features"></a>何时禁用所需的功能

经过训练的应用可能会基于以下某种情况对机器学习实体做出错误预测：
* 示例言语的标记错误。
* 所需的功能与文本不匹配。

例如，一个机器学习实体具有一个子实体，该子实体是某个人的姓名。

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="具有所需功能的 LUIS 门户机器学习实体架构的屏幕截图":::

此机器学习实体的示例言语为：`Assign Bob Jones to work on the new security feature`。

提取应是 `security feature` 作为票证说明，`Bob Jones` 作为工程师，它们是 `Assign ticket` 实体的两个子实体。

为了帮助子实体成功预测，请将预生成实体 [PersonName](luis-reference-prebuilt-person.md) 作为功能添加到 `engineer` 子实体。 如果你将该功能设为必需，这意味着只有在为文本预测 PersonName 预生成实体时，才会提取子实体。 这意味着文本中未使用 PersonName 子实体预测的任何姓名都不会作为标记的子实体 `engineer` 返回。

使用交互式测试窗格，并看到具有所需功能的子实体未进行预测时，请切换此设置，查看是否可以在不需要该功能的情况下预测子实体。 由于对示例言语进行了正确标记，可能能够在没有所需功能的情况下正确预测子实体。

## <a name="view-sentiment-results"></a>查看情绪结果

如果在[发布](luis-how-to-publish-app.md#enable-sentiment-analysis)页面上配置了“情绪分析”，则测试结果会包括在该陈述中发现的情绪 。

## <a name="correct-matched-patterns-intent"></a>更正匹配的模式的意向

如果使用[模式](luis-concept-patterns.md)并且该陈述与某个模式匹配，但是意向预测错误，请选择该模式旁边的“编辑”链接，然后选择正确的意向。

## <a name="compare-with-published-version"></a>与已发布的版本进行比较

可以使用已发布的[终结点](luis-glossary.md#endpoint)版本测试应用的活动版本。 在“检查”面板中选择“与已发布版本进行比较” 。 针对该发布模型的任何测试都会从 Azure 订阅配额余量中扣除。

> [!div class="mx-imgBorder"]
> ![与已发布版本进行比较](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>在测试面板中查看终结点 JSON
选择“显示 JSON 视图”，可以查看该比较返回的终结点 JSON。

> [!div class="mx-imgBorder"]
> ![已发布的 JSON 响应](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>测试面板中的其他设置

### <a name="luis-endpoint"></a>LUIS 终结点

如果有多个 LUIS 终结点，请使用测试的“已发布”窗格上的“其他设置”链接来更改用于测试的终结点。 如果不确定要使用哪个终结点，请选择默认的“Starter_Key”。

> [!div class="mx-imgBorder"]
> ![突出显示了“其他设置”链接的测试面板](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>批处理测试
请参阅批处理测试[概念](./luis-how-to-batch-test.md)并了解陈述批量测试的[方法](luis-how-to-batch-test.md)。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的准确性。

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endpoint-utterances.md)
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md)