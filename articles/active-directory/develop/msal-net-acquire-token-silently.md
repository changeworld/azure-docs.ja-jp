---
title: トークンを自動的に取得する (Microsoft Authentication Library for .NET) | Azure
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して、アクセス トークンを自動的に取得する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544234"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET を使用してトークン キャッシュからトークンを取得する

Microsoft Authentication Library for .NET (MSAL.NET) を使用してアクセス トークンを取得すると、トークンはキャッシュされます。 トークンが必要な場合に、アプリケーションは最初に `AcquireTokenSilent` メソッドを呼び出して、キャッシュ内に利用可能なトークンがあるか確認します。 多くの場合、キャッシュ内のトークンに基づいて、より多くのスコープを備える別のトークンが取得されます。 また、期限切れが近いトークンを更新することもできます (トークン キャッシュには更新トークンも含まれるため)。

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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