---
title: キャッシュからトークンを取得する (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して、アクセス トークンを自動的に取得する方法について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67a72b295005e328723be5d3b577d15330c05134
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424271"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET を使用してトークン キャッシュからトークンを取得する

Microsoft Authentication Library for .NET (MSAL.NET) を使用してアクセス トークンを取得すると、トークンはキャッシュされます。 トークンが必要な場合に、アプリケーションは最初に `AcquireTokenSilent` メソッドを呼び出して、キャッシュ内に利用可能なトークンがあるかどうかを確認します。 多くの場合、キャッシュ内のトークンに基づいて、より多くのスコープを備える別のトークンが取得されます。 また、期限切れが近いトークンを更新することもできます (トークン キャッシュには更新トークンも含まれるため)。

推奨されるパターンは、最初に `AcquireTokenSilent` メソッドを呼び出すことです。  `AcquireTokenSilent` が失敗した場合は、他の方法を使用してトークンを取得します。

次の例では、アプリケーションはまずトークン キャッシュからトークンを取得しようとします。  `MsalUiRequiredException` 例外がスローされると、アプリケーションは対話的にトークンを取得します。 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
