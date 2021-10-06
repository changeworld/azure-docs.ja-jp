---
title: MSAL.NET でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL.NET でのエラーと例外をログに記録する方法について説明します。
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
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091865"
---
# <a name="logging-in-msalnet"></a>MSAL.NET でのログ

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>MSAL.NET でのログの構成

MSAL では、ログ記録は、`.WithLogging` ビルダー修飾子を使用したアプリケーションの作成時に設定されます。 このメソッドは、次のオプション パラメーターを取ります。

- `Level` では、目的のログ記録のレベルを指定できます。 エラーに設定すると、エラーだけを記録します
- `PiiLoggingEnabled` を true に設定した場合、個人および組織のデータ (PII) をログ記録できます。 既定では、false に設定されており、そのためアプリケーションは個人データをログ記録しません。
- `LogCallback` は、ログ記録を行う委任に設定されます。 `PiiLoggingEnabled` が true の場合、このメソッドは PII を持つことができるメッセージを受信します。この場合、`containsPii` フラグは true に設定されます。
- `DefaultLoggingEnabled` は、プラットフォームの既定のログ記録を有効にします。 既定では、false になっています。 これを true に設定した場合、デスクトップ/UWP アプリケーションのイベント トレーシング、iOS の NSLog、および Android の logcat を使用します。

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
 > MSAL.NET のログ記録のサンプルなどについては、[MSAL.NET の Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) を参照してください。

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。
