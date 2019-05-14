---
title: 他の Web API を呼び出す Web API (アプリのトークンの取得) - Microsoft ID プラットフォーム
description: 他の Web API を呼び出す Web APIを構築する方法について説明します (アプリのトークンの取得)
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
ms.openlocfilehash: ada4323b0e61c6dc9fb87af133c40ec9c35e3834
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080115"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API を呼び出す Web API - アプリのトークンの取得

クライアント アプリケーション オブジェクトを構築したら、それを使用して、Web API を呼び出すために使用できるトークンを取得します。

## <a name="code-in-the-controller"></a>コントローラーのコード

ダウンストリーム API (todolist という名前) を呼び出す API コントローラーのアクションで呼び出されるコードの例を以下に示します。

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` は、記事「[Web API を呼び出す Web API - アプリの構成](scenario-web-api-call-api-app-configuration.md)」で説明されているものと類似しています。 `BuildConfidentialClient()` は、1 つのアカウントの情報のみが含まれているキャッシュを使用して `IConfidentialClientApplication` をインスタンス化します。 アカウントは、`GetAccountIdentifier` メソッドによって提供されます。

`GetAccountIdentifier` メソッドは、Web API で JWT を受信したユーザーの ID に関連付けられた要求を使用します。

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Web API の呼び出し](scenario-web-api-call-api-call-api.md)
