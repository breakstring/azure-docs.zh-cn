---
title: 人为标记的听录准则 - 语音服务
titleSuffix: Azure Cognitive Services
description: 为了提高特定情况下（例如在删除或错误替代单词的情况下）语音识别的准确度，可以对音频数据使用人为标记的听录。 人为标记的听录是对音频文件进行的逐字/词听录。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: db3d8f4424f59d8432221753af776a5b55859882
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388849"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>如何创建人为标记的听录

若要提高特定情况下（尤其是在因删除或错误替代单词而导致问题的情况下）的识别准确度，需要对音频数据使用人为标记的听录。 什么是人为标记的听录？ 很简单，人为标记的听录是对音频文件进行的逐字/词听录。

若要改善识别，建议使用大量的脚本数据，建议提供10到20小时的脚本数据。 在此页上，我们将查看旨在帮助你创建高质量听录的准则。 本指南按区域设置划分为“美国英语”、“中国大陆普通话”和“德语”三部分。

> [!NOTE]
> 并非所有基本模型都支持音频文件的自定义。 如果基本模型不支持该模型，则训练将使用与使用相关文本相同的方式来使用转录的文本。 有关支持音频数据定型的基本模型的列表，请参阅 [语言支持](language-support.md#speech-to-text) 。

> [!NOTE]
> 如果更改了用于定型的基本模型，并在定型数据集中具有音频，请 *始终* 检查新选择的基本模型是否 [支持音频数据定型](language-support.md#speech-to-text)。 如果以前使用的基本模型不支持对音频数据进行定型，而定型数据集包含音频，则新基础模型的定型时间将会 **大幅** 增加，并且可能会轻松地从几个小时到几天及更长时间。 如果你的语音服务订阅 **不** 在 [具有专用硬件](custom-speech-overview.md#set-up-your-azure-account) 培训的区域中，则更是如此。
>
> 如果你面对上述段落中所述的问题，则可以通过减少数据集中的音频量或完全删除该数据并仅留下文本，来快速缩短定型时间。 如果你的语音服务订阅 **不** 在 [具有专用硬件](custom-speech-overview.md#set-up-your-azure-account) 培训的区域中，则强烈建议使用后一种方法。

## <a name="us-english-en-us"></a>美国英语 (en-US)

英语音频的人为标记的听录必须以纯文本形式提供，仅使用 ASCII 字符。 避免使用拉丁语-1 或 Unicode 标点字符。 从文字处理应用程序中复制文本或从网页中擦除数据时，常常会无意中添加这些字符。 如果存在这些字符，请务必将其更新为相应的 ASCII 替代字符。

以下是一些示例：

| 要避免的字符 | 替换 | 说明 |
| ------------------- | ------------ | ----- |
| “Hello world” | “Hello world” | 左引号和右引号都已替换为相应的 ASCII 字符。 |
| John’s day | John's day | 撇号已替换为相应的 ASCII 字符。 |
| it was good—no, it was great! | it was good--no, it was great! | 长划线已替换为两个连字符。 |

### <a name="text-normalization-for-us-english"></a>美国英语的文本规范化

文本规范化是指将字词转换为在训练模型时使用的一致格式。 某些规范化规则会自动应用到文本，但我们建议你在准备人为标记的听录数据时遵循以下准则：

- 将缩写写成字词。
- 将非标准数字字符串写成字词（例如会计术语）。
- 应按照发音听录非字母字符或混合字母数字字符。
- 不应编辑可以作为字词发音的缩写（例如，“radar”、“laser”、“RAM”或“NATO”）。
- 将发音的缩写写成单独的字母，每个字母用单个空格分开。
- 如果使用音频，转录数字作为与音频 (匹配的单词（例如 "101"），则可能会被视为 "1 0 1" 或 "101" ) 。
- 避免重复字符、单词或单词组超过三次，如 "是" 是 "是"。 语音服务可能会删除具有此重复的行。

下面是应该对听录执行的规范化的一些示例：

| 原始文本               | 规范化后的文本              |
| --------------------------- | ------------------------------------- |
| Bruce 横幅            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+         | My blood type is O positive           |
| Water is H20                | Water is H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

以下规范化规则自动应用到听录：

- 使用小写字母。
- 删除除字词中撇号外的所有标点。
- 将数字扩展为字词/口语形式，例如美元金额。

下面是自动对听录执行的规范化的一些示例：

| 原始文本                          | 规范化后的文本          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It costs \$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>中国大陆普通话 (zh-CN)

中国大陆普通话音频的人为标记的听录必须使用字节顺序标记进行 UTF-8 编码。 避免使用半角标点字符。 在文字处理程序中准备数据或从网页中擦除数据时，可能会无意中包括这些字符。 如果存在这些字符，请务必将其更新为相应的全角替代字符。

以下是一些示例：

| 要避免的字符 | 替换   | 说明 |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | 左引号和右引号都已替换为相应的字符。 |
| 需要什么帮助? | 需要什么帮助？| 问号已替换为相应的字符。 |

### <a name="text-normalization-for-mandarin-chinese"></a>中国大陆普通话的文本规范化

文本规范化是指将字词转换为在训练模型时使用的一致格式。 某些规范化规则会自动应用到文本，但我们建议你在准备人为标记的听录数据时遵循以下准则：

- 将缩写写成字词。
- 用口语形式写数字字符串。

下面是应该对听录执行的规范化的一些示例：

| 原始文本 | 规范化后的文本 |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

以下规范化规则自动应用到听录：

- 删除所有标点
- 将数字扩展为口语形式
- 将全角字母转换为半角字母
- 对所有英语单词使用大写字母

下面是自动对听录执行的规范化的一些示例：

| 原始文本 | 规范化后的文本 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ￥ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>德语 (de-DE) 和其他语言

德语（以及其他既非英语也非中国大陆普通话的语言）音频的人为标记的听录必须使用字节顺序标记进行 UTF-8 编码。 应该为每个音频文件提供一个人为标记的听录。

### <a name="text-normalization-for-german"></a>德语文本规范化

文本规范化是指将字词转换为在训练模型时使用的一致格式。 某些规范化规则会自动应用到文本，但我们建议你在准备人为标记的听录数据时遵循以下准则：

- 将小数点写为“,”，而不是“.”。
- 写入时间分隔符为 "："，而不是 "." (例如： 12:00 Uhr) 。
- 不替换“ca.” 等缩写。 我们建议使用完整的口语形式。
- 删除四个主要的数学运算符（+、-、\* 和 /）。 我们建议将其替换为文字形式：“plus”、“minus”、“mal”、“geteilt”。
- 删除比较运算符（=、< 和 >）。 我们建议其替换为“gleich”、“kleiner als”和“grösser als”。
- 将分数（例如 3/4）写成文字形式（例如，写成“drei viertel”而不是 3/4）。
- 将“€”符号替换为文字形式“Euro”。

下面是应该对听录执行的规范化的一些示例：

| 原始文本    | 用户规范化后的文本 | 系统规范化后的文本       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

以下规范化规则自动应用到听录：

- 对所有文本使用小写字母。
- 删除所有标点，包括多种引号（可以保留 "test"、'test'、"test„ 以及 «test»）。
- 删除包含下述任一特殊字符的行：¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬。
- 将数字扩展为口语形式，包括美元或欧元金额。
- 仅接受 a、o、u 的元音变音符。 其余将替换为 th 或被丢弃。

下面是自动对听录执行的规范化的一些示例：

| 原始文本    | 规范化后的文本 |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>日语文本规范化

在日语 (ja-jp) 中，每个句子的最大长度为90个字符。 带有较长句子的行将被丢弃。 若要添加较长的文本，请在 "介于" 之间插入句点。

## <a name="next-steps"></a>后续步骤

- [准备和测试数据](./how-to-custom-speech-test-and-train.md)
- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)
- [训练模型](how-to-custom-speech-train-model.md)
- [部署模型](./how-to-custom-speech-train-model.md)