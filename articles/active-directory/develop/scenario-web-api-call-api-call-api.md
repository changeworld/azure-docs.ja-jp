---
title: Web API を呼び出す Web API - Microsoft ID プラットフォ―ム | Azure
description: Web API を呼び出す Web API を構築する方法について説明します。
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
ms.openlocfilehash: 701f1f7c26731f3e9653955907f5f16d2688cdb2
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043445"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Web API を呼び出す Web API: API を呼び出す

トークンを取得した後に、保護された Web API を呼び出せます。 これは、ASP.NET または ASP.NET Core の Web API のコントローラーから行います。

## <a name="controller-code"></a>コントローラー コード

次のコードは、「[Web API を呼び出す Web API: アプリのトークンを取得する](scenario-web-api-call-api-acquire-token.md)」で示されているコード例の続きです。 このコードは、API コントローラーのアクションで呼び出されます。 *todolist* という名前のダウンストリーム API を呼び出します。

トークンを取得した後、ダウンストリーム API を呼び出すベアラー トークンとしてそれを使用します。

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web API を呼び出す Web API: 運用環境に移行する](scenario-web-api-call-api-production.md)
