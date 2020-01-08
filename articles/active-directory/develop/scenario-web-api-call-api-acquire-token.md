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
ms.openlocfilehash: cc92fb7bc5ddf451279e6c157f9e93aa7fe9a12a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423616"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API を呼び出す Web API - アプリのトークンの取得

クライアント アプリケーション オブジェクトを構築したら、それを使用して、Web API を呼び出すために使用できるトークンを取得します。

## <a name="code-in-the-controller"></a>コントローラーのコード

ダウンストリーム API (todolist という名前) を呼び出す API コントローラーのアクションで呼び出されるコードの例を以下に示します。

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

`BuildConfidentialClient()` は、記事「[Web API を呼び出す Web API - アプリの構成](scenario-web-api-call-api-app-configuration.md)」で説明されているものと類似しています。 `BuildConfidentialClient()` は、1 つのアカウントの情報のみが含まれているキャッシュを使用して `IConfidentialClientApplication` をインスタンス化します。 アカウントは、`GetAccountIdentifier` メソッドによって提供されます。

`GetAccountIdentifier` メソッドは、Web API で JWT を受信したユーザーの ID に関連付けられた要求を使用します。

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
> [Web API の呼び出し](scenario-web-api-call-api-call-api.md)
