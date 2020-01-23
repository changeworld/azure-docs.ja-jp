---
title: Web API を呼び出す Web API のトークンを取得する | Azure
titleSuffix: Microsoft identity platform
description: アプリのトークンを取得する必要がある Web API を呼び出す Web API を構築する方法について説明します。
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
ms.openlocfilehash: 9cf660cbf981079ca20111e34fcd34504d8dcbfb
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044126"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Web API を呼び出す Web API: アプリのトークンを取得する

クライアント アプリケーション オブジェクトを構築した後、それを使用して、Web API を呼び出すために使用できるトークンを取得します。

## <a name="code-in-the-controller"></a>コントローラーのコード

API コントローラーのアクションで呼び出されるコードの例を次に示します。 *todolist* という名前のダウンストリーム API を呼び出します。

```csharp
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

`BuildConfidentialClient()` は、「[Web API を呼び出す Web API: アプリの構成](scenario-web-api-call-api-app-configuration.md)」のシナリオに似ています。 `BuildConfidentialClient()` では、1 つのアカウントの情報のみが含まれているキャッシュを使用して、`IConfidentialClientApplication` がインスタンス化されます。 アカウントは、`GetAccountIdentifier` メソッドによって提供されます。

`GetAccountIdentifier` メソッドでは、Web API で JSON Web トークン (JWT) を受け取ったユーザーの ID に関連付けられている要求が使用されます。

```csharp
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web API を呼び出す Web API: API を呼び出す](scenario-web-api-call-api-call-api.md)
