---
title: 呼叫（语音/视频）和聊天的定价方案
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的定价模型。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 779fa577b25bd4f2aa92aa8b8cc1244a58bdf1ae
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126184"
---
# <a name="pricing-scenarios"></a>定价方案

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure 通信服务的价格通常基于即用即付模型。

## <a name="voicevideo-calling-and-screen-sharing"></a>语音/视频呼叫和屏幕共享

Azure 通信服务允许向应用程序添加语音/视频呼叫和屏幕共享。 你可以使用 JavaScript、Objective-C (Apple)、Java (Android) 或 .NET 客户端库在应用程序中嵌入该体验。 请参阅我们的[可用客户端库完整列表](./sdk-options.md)。

### <a name="pricing"></a>定价

对于群呼，呼叫和屏幕共享按每个参与者每分钟 0.004 美元进行收费。 若要了解各种可能的呼叫流，请参阅[此页面](./call-flows.md)。

呼叫的每个参与者连接到呼叫的每一分钟都会计入费用。 无论用户是进行视频呼叫、语音呼叫还是屏幕共享，情况都是如此。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>定价示例：使用 JS 和 iOS 客户端库的音频/视频群呼

Alice 与其同事 Bob 和 Charlie 进行了一次群呼。 Alice 和 Bob 使用 JS 客户端库，Charlie 使用 iOS 客户端库。

- 该呼叫总共持续 60 分钟。
- Alice 和 Bob 参与了整个呼叫。 Alice 将其视频打开了 5 分钟，并将其屏幕共享了 23 分钟。 Bob 在整个呼叫期间（60 分钟）都打开了其视频，并将其屏幕共享了 12 分钟。
- Charlie 在 43 分钟之后离开了呼叫。 Charlie 在其参与的持续时间（43 分钟）内使用了音频和视频。

成本计算

- 2 个参与者 x 60 分钟 x 0.004 美元/参与者/分钟 = 0.48 美元 [视频和音频以相同费率进行收费]
- 1 个参与者 x 43 分钟 x 0.004 美元/参与者/分钟 = 0.172 美元 [视频和音频以相同费率进行收费]

群呼的总成本：0.48 美元 + 0.172 美元 = 0.652 美元

## <a name="chat"></a>聊天

借助通信服务，你可以利用在 2 个或更多用户之间发送和接收聊天消息的功能来增强应用程序。 聊天客户端库适用于 JavaScript、.NET、Python 和 Java。 请参阅[此页面以了解客户端库](./sdk-options.md)

### <a name="price"></a>价格

发送每条聊天消息的费用为 0.0008 美元。

### <a name="pricing-example-chat-between-two-users"></a>定价示例：两个用户之间的聊天 

Geeta 启动了与 Emily 的聊天线程以共享更新并发送 5 条消息。 聊天持续了 10 分钟，其中 Geeta 和 Emily 相互发送了 15 条消息。

成本计算 
- 发送的消息数 (5 + 15 + 15) x 0.0008 美元 = 0.028 美元

### <a name="pricing-example-group-chat-with-multiple-users"></a>定价示例：与多个用户进行的群聊 

Charlie 启动了与好友 Casey 和 Jasmine 的聊天线程以规划休假。 他们聊了一段时间，其中 Charlie、Casey 和 Jasmine 分别发送了 20、30 和 18 条消息。 他们意识到其好友 Rose 也可能会对加入旅行感兴趣，因此将她添加到聊天线程中，并与她共享所有消息历史记录。 

Rose 会看到消息并开始聊天。 与此同时，Casey 收到了一个电话，决定稍后再进行对话。 Charlie、Jasmine 和 Rose 决定了旅行日期，并分别发送了另外 30、25、35 条消息。

成本计算 

- 发送的消息数 (20 + 30 + 18 + 30 + 25 + 35) x 0.0008 美元 = 0.1264 美元


## <a name="telephony-and-sms"></a>电话服务和短信

## <a name="price"></a>价格 

电话服务按每分钟计费，短信服务按每条短信计费。 定价取决于所用号码的类型和位置以及呼叫和短信的目的地。

### <a name="telephone-number-leasing"></a>电话号码租赁

先收取电话号码租赁预付费用，然后按月定期收费：

|数字类型   |每月费用   |
|--------------|-----------|
|本地（美国）     |1 美元/月        |
|免费电话（美国） |2 美元/月 |


### <a name="telephone-calling"></a>电话呼叫

对于美国境内的电话号码，传统电话呼叫（通过公用电话交换网进行的呼叫）提供即用即付定价。 价格为基于所用号码类型和呼叫目的地的每分钟费用。 下表列出了最受欢迎呼叫目的地的价格详细信息。 有关目的地的完整列表，请参阅[详细定价列表](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)。


#### <a name="united-states-calling-prices"></a>美国呼叫价格

以下价格包含了 2021 年 6 月 30 日之前所需的通信税和费用：

|数字类型   |拨打电话   |接听电话|
|--------------|-----------|------------|
|Local     |每分钟 0.013 美元起       |每分钟 0.0085 美元        |
|免费 |每分钟 0.013 美元   |每分钟 0.0220 美元 |

#### <a name="other-calling-destinations"></a>其他呼叫目的地

以下价格包含了 2021 年 6 月 30 日之前所需的通信税和费用：

|呼叫目的地   |每分钟价格|
|-----------|------------|
|加拿大     |每分钟 0.013 美元起   |
|英国     |每分钟 0.015 美元起   |
|德国     |每分钟 0.015 美元起   |
|法国     |每分钟 0.016 美元起   |


### <a name="sms"></a>SMS

短信提供即用即付定价。 价格为基于短信目的地的每条短信费用。 可以通过免费电话号码向美国境内的电话号码发送短信。 请注意，本地（地理）电话号码不能用于发送短信。

以下价格包含了 2021 年 6 月 30 日之前所需的通信税和费用：

|国家/地区   |发送消息|接收消息|
|-----------|------------|------------|
|美国（免费）    |每条短信 0.0075 美元   | 每条短信 0.0075 美元 |
