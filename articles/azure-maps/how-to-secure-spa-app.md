---
title: 非対話型のサインインを使用してシングルページ Web アプリケーションを Microsoft Azure Maps 内でセキュリティで保護する方法
titleSuffix: Azure Maps
description: 非対話型の Azure ロールベースのアクセス制御 (Azure RBAC) と Azure Maps Web SDK を使用してシングルページ Web アプリケーションを構成する方法。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 8f3249cec073e7a1fd6fdabd6a7af24ce5a2c2d2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443587"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>非対話型サインインでシングルページ Web アプリケーションをセキュリティで保護する方法

この記事では、ユーザーが Azure Active Directory にサインインできない場合に Azure Active Directory (Azure AD) を使用してシングルページ Web アプリケーションをセキュリティで保護する方法について説明します。

この非対話型認証フローを作成するには、Azure 関数のセキュリティで保護された Web サービスを作成します。この Web サービスが、Azure Active Directory からアクセス トークンを取得する役割を担います。 この Web サービスは、シングルページ Web アプリケーションでのみ使用できます。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps は、ユーザーのサインオンまたは対話型フローからのアクセス トークンをサポートできます。 対話型フローを使用すると、アクセスの失効とシークレット管理の範囲を制限できます。

## <a name="create-an-azure-function"></a>Azure 関数の作成

Azure Active Directory の認証を行うセキュリティで保護された Web サービス アプリケーションを作成するには、次の手順を実行します。

1. Azure portal で関数を作成します。 詳細については、「[Azure Functions の概要](../azure-functions/functions-get-started.md)」を参照してください。

2. シングルページ Web アプリケーションからアクセスできるように、Azure 関数で CORS ポリシーを構成します。 CORS ポリシーによって、ブラウザー クライアントが Web アプリケーションの許可された配信元に安全にアクセスできるようになります。 詳細については、「[CORS 機能の追加](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)」を参照してください。

3. Azure 関数に[システム割り当て ID を追加](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)して、Azure AD に認証するサービス プリンシパルを作成できるようにします。  

4. Azure Maps アカウントにシステム割り当て ID に対するロールベースのアクセス権を付与します。 詳細については、「[ロールベースのアクセス権を付与する](#grant-role-based-access-for-users-to-azure-maps)」を参照してください。

5. サポートされているいずれかのメカニズムまたは REST プロトコルを使用して、システム割り当て ID を使用して Azure Maps アクセス トークンを取得する Azure 関数のコードを記述します。 詳細については、「[Azure リソースのトークンの取得](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)」を参照してください。

    REST プロトコルの例を次に示します。

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    応答の例を次に示します。

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Azure 関数 HttpTrigger のセキュリティを次のように構成します。

   1. [関数アクセス キーを作成する](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   1. 運用環境の Azure 関数の [HTTP エンドポイントをセキュリティで保護](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)する。

7. Web アプリケーション Azure Maps Web SDK を構成します。

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://{App-Name}.azurewebsites.net/api/{Function-Name}?code={API-Key}';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>次のステップ

シングルページ アプリケーションのシナリオの詳細については、次を参考にしてください。
> [!div class="nextstepaction"]
> [シングルページ アプリケーション](../active-directory/develop/scenario-spa-overview.md)

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示す他のサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
