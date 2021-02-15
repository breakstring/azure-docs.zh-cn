---
title: 跟踪严重性比下降 - Azure Application Insights
description: 使用 Azure Application Insights 监视应用程序跟踪，以通过智能检测了解跟踪遥测中的异常模式。
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539867"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>跟踪严重性比下降（预览）

跟踪广泛应用于应用程序，因为它们可以告知幕后发生的事情。 出现错误时，跟踪可保证导致不理想状态事件序列的关键可见性。 虽然跟踪通常为非结构化，但通过跟踪可以具体了解到一件事情，即严重性级别。 在应用程序稳定状态下，“良好”跟踪（Info 和 Verbose）和“错误”跟踪（Warning、Error 和 Critical）之间的比率应保持稳定      。 假设情况是不管出于什么情况（例如暂时性网络故障），在一定的程度都会定期发生“错误”跟踪。 但当一个真正的问题日益严重时，这通常表现为“错误”跟踪和“良好”跟踪之间的相关比率增大。 Application Insights 智能检测自动分析应用程序记录的跟踪，并在跟踪遥测的严重性级别出现异常时发出警告。

此功能需要为应用配置跟踪日志（请参见如何为 [.NET](./asp-net-trace-logs.md) 或 [Java](./java-trace-logs.md) 配置跟踪日志侦听器），除此之外，不需要其他特殊步骤。 在应用生成足够多的异常遥测数据后，此功能会激活。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何时会收到此类型的智能检测通知？
对比前面七天计算的基线，如果“良好”跟踪（跟踪级别记录为 Info 或 Verbose）和“错误”跟踪（跟踪级别记录为 Warning、Error 或 Fatal）之间的比率在某一天降低，则可能会收到此类型通知      。

## <a name="does-my-app-definitely-have-a-problem"></a>收到通知是否意味着我的应用肯定有问题？
不是，通知并不意味着应用肯定有问题。 虽然“良好”和“错误”跟踪之间的比率降低可能指示应用程序出现问题，但这种比率的变化也可能是良性的。 例如，如果应用程序中的新流发出的“错误”跟踪多于现有流，则可能带来比率的增加。

## <a name="how-do-i-fix-it"></a>如何解决问题？
通知包括诊断信息，以在诊断进程中提供支持：
1. **会审。** 通知会显示有多少操作受到影响。 这可以帮助你对问题分配优先级。
2. **划分范围。** 该问题是影响所有流量，还是只影响某些操作？ 可以从通知中获取此信息。
3. **诊断。** 可以使用链接到支持信息的相关项和报告，帮助进一步诊断问题。
