---
title: 记录 MSAL.NET 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何在 MSAL.NET 中记录错误和异常
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954828"
---
# <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>在 MSAL.NET 中配置日志记录

在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- `Level` 用于确定你需要哪种级别的日志记录。 将其设置为“Errors”时，就只会获得错误
- `PiiLoggingEnabled` 在设置为 true 的情况下可以记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` 设置为一个执行日志记录的委托。 如果 `PiiLoggingEnabled` 为 true，则此方法会接收消息两次：第一次时 `containsPii` 参数为 false，消息没有个人数据；第二次时 `containsPii` 参数为 true，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。
- `DefaultLoggingEnabled` 为平台启用默认日志记录。 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > 有关 MSAL.NET 日志记录的示例和其他信息，请参阅 [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。