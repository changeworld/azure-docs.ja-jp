---
title: Web API を呼び出す Web アプリ (Web API の呼び出し) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリの構築方法について説明します (Web API の呼び出し)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080069"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Web API を呼び出す Web アプリ - Web API の呼び出し

トークンを取得したので、保護された Web API を呼び出すことができます。

## <a name="aspnet-core"></a>ASP.NET Core

以下に、`HomeController` のアクションの簡略化されたコードを示します。 このコードは、Microsoft Graph を呼び出すトークンを取得します。 今回コードが追加されて、Microsoft Graph を REST API として呼び出す方法が示されています。

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> 同じ原則を使用するとどの web API も呼び出すことができます。
>
> ほとんどの Azure の Web API には、呼び出しを簡略化する SDK が用意されています。 これは、Microsoft Graph の場合も同じです。 次の記事では、これらの側面を示すチュートリアルの場所について説明します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-call-api-production.md)
