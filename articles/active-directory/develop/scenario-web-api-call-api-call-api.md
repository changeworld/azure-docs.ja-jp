---
title: Web API を呼び出す Web API - Microsoft ID プラットフォ―ム | Azure
description: Web API を呼び出す Web API を構築する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885091"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Web API を呼び出す Web API: API を呼び出す

トークンを取得した後に、保護された Web API を呼び出せます。 これを行うには、Web API のコントローラーを使用します。

## <a name="controller-code"></a>コントローラー コード

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

# <a name="java"></a>[Java](#tab/java)

次のコードは、「[Web API を呼び出す Web API: アプリのトークンを取得する](scenario-web-api-call-api-acquire-token.md)」で示されているコード例の続きです。 このコードは、API コントローラーのアクションで呼び出されます。 ダウンストリーム API MS Graph を呼び出します。

トークンを取得した後、ダウンストリーム API を呼び出すベアラー トークンとしてそれを使用します。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
このフローを MSAL Python でデモンストレーションするサンプルはまだ使用できません。

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web API を呼び出す Web API: 運用環境に移行する](scenario-web-api-call-api-production.md)
