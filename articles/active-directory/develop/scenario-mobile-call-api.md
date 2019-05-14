---
title: Web API を呼び出すモバイル アプリ - Web API の呼び出し | Microsoft ID プラットフォーム
description: Web API を呼び出すモバイル アプリ (Web API の呼び出し) のビルド方法について学習します
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080133"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Web API を呼び出すモバイル アプリ - Web API の呼び出し

アプリがユーザーにサインインして、トークンを受信すると、ユーザーとその環境、および発行されたトークンに関するいくつかの情報が MSAL によって公開されます。 アプリではこれらの値が使用され、Web API を呼び出したり、ウェルカム メッセージをユーザーに表示したりすることができます。

最初に、MSAL の結果について説明し、次に、`AuthenticationResult` または `result` からのアクセス トークンを使用して保護された Web API を呼び出す方法を説明します。

## <a name="msal-result"></a>MSAL の結果

- `AccessToken`:HTTP ベアラー要求で保護された Web API の呼び出しに使用します。
- `IdToken`:サインインしたユーザーに関する有用な要求 (名前、ホーム テナント、ストレージの一意識別子など) が含まれます。
- `ExpiresOn`: トークンの有効期限。 MSAL はアプリの自動更新を処理します。
- `TenantId`:サインインに使用されるユーザーのテナントの識別子。 ゲスト ユーザー (Azure AD B2B) の場合、これはユーザーのホーム テナントではなく、ユーザーがサインインしたテナントになります。  
- `Scopes`: トークンで付与されたスコープ。 これは要求内容のサブセットである場合があります。

さらに、MSAL では `Account` の抽象化も提供されます。 アカウントは、現在のユーザーのサインインしたアカウントを表します。

- `HomeAccountIdentifier`:ユーザーのホーム テナントの識別子。
- `UserName`:ユーザーの推奨ユーザー名。 Azure AD B2C ユーザーの場合、これは空の場合があります。
- `AccountIdentifier`:サインインしているユーザーの識別子。 ユーザーが別のテナントのゲストではない限り、ほとんどの場合、これは `HomeAccountIdentifier` と同じになります。

## <a name="calling-an-api"></a>API の呼び出し

アクセス トークンが準備できたら、Web API を呼び出すのは簡単です。 アプリはこのトークンを取得して、HTTP 要求を構築し、それを実行します。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put Access token in HTTP Request
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>複数の API 要求を行う

同じ API を複数回、または複数の API を呼び出す必要がある場合は、アプリをビルドするときに追加の考慮事項があります。

- ***増分同意***:Microsoft ID プラットフォームでは、すべて事前にではなく、必要に応じて、アプリがユーザーの同意をアクセス許可として得られるようにしています。 アプリが API を呼び出す準備ができたら、毎回、使用する予定のスコープのみを要求します。
- ***条件付きアクセス***:特定のシナリオでは、複数の API 要求を行うときに、追加の条件付きアクセスの要件が課される場合があります。 このシナリオを処理するため、必ずサイレント要求からのエラーをキャッチし、対話型の要求を行えるように準備します。 これは、最初の要求に条件付きアクセス ポリシーが適用されておらず、アプリが条件付きアクセスを必要とする新しい API にサイレントでアクセスしようとする場合に発生することがあります。 詳細については、[条件付きアクセスについてのガイダンス](conditional-access-dev-guide.md)のページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境に移行する](scenario-mobile-production.md)
