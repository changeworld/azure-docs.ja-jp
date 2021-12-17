---
title: AuthN/AuthZ での OAuth トークン
description: App Service の組み込みの認証と認可を使用しているときに、トークンを取得したり、トークンを更新したり、セッションを拡張したりする方法について説明します。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f158f9f76820635a65737b75f3c016ff67a3a92a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352077"
---
# <a name="work-with-oauth-tokens-in-azure-app-service-authentication"></a>Azure App Service 認証で OAuth トークンを操作する

この記事では、[App Service の組み込みの認証と認可](overview-authentication-authorization.md) を使用しているときに OAuth トークンを操作する方法について説明します。 

## <a name="retrieve-tokens-in-app-code"></a>アプリ コードでのトークンの取得

サーバー コードからプロバイダー固有のトークンが要求ヘッダーに挿入されるので、これらのトークンに簡単にアクセスできます。 次の表は、可能なトークン ヘッダー名を示しています。

| プロバイダー | ヘッダー名 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook トークン | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

> [!NOTE]
> これらのヘッダーは、さまざまな言語フレームワークによって異なる形式 (小文字や先頭文字が大文字など) でアプリ コードに提供される可能性があります。

クライアント コード (モバイル アプリやブラウザー内の JavaScript など) から、HTTP `GET` 要求を `/.auth/me` に送信します ([トークン ストア](overview-authentication-authorization.md#token-store)を有効にする必要があります)。 返される JSON にはプロバイダー固有のトークンがあります。

> [!NOTE]
> アクセス トークンはプロバイダー リソースへのアクセス用であるため、クライアント シークレットを使用してプロバイダーを構成する場合にのみ存在します。 更新トークンを取得する方法を確認するには、「更新アクセス トークン」を参照してください。

## <a name="refresh-auth-tokens"></a>認証トークンを更新する

プロバイダーのアクセス トークン ([セッション トークン](#extend-session-token-expiration-grace-period)ではなく) が期限切れになった場合は、そのトークンを再度使用する前に、ユーザーを再認証する必要があります。 アプリケーションの `/.auth/refresh` エンドポイントに `GET` 呼び出しを行って、トークンの期限切れを回避することができます。 呼び出されると、App Service は認証されたユーザーの[トークン ストア](overview-authentication-authorization.md#token-store)内のアクセス トークンを自動的に更新します。 アプリ コードによる後続のトークン要求で、更新トークンを取得します。 ただし、トークンの更新が動作するためには、トークン ストアにプロバイダーの[更新トークン](https://auth0.com/learn/refresh-tokens/)が含まれている必要があります。 更新トークンの取得方法は各プロバイダーによって文書化されていますが、次の一覧に概要を示します。

- **Google**: `access_type=offline` クエリ文字列パラメーターを `/.auth/login/google` API 呼び出しに追加します。 Mobile Apps SDK を使用している場合は、`LogicAsync` オーバーロードの 1 つにパラメーターを追加できます ([Google 更新トークン](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)に関するページをご覧ください)。
- **Facebook**: 更新トークンを提供しません。 長期間維持されるトークンの有効期限は 60 日間です ([Facebook のアクセス トークンの有効期限と延長](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)に関するページをご覧ください)。
- **Twitter**: アクセス トークンに有効期限はありません ([Twitter OAuth の FAQ](https://developer.twitter.com/en/docs/authentication/faq) に関するページを参照してください)。
- **Microsoft**: [https://resources.azure.com](https://resources.azure.com) で、次の手順を実行します。
    1. ページの上部にある **[Read/Write]** を選択します。
    2. 左側のブラウザーで、**subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettingsV2** の順に移動します。
    3. **[編集]** をクリックします。
    4. 次のプロパティを変更します。

        ```json
        "identityProviders": {
          "azureActiveDirectory": {
            "login": {
              "loginParameters": ["scope=openid profile email offline_access"]
            }
          }
        }
        ```

    5. **[Put]** をクリックします。
    
    > [!NOTE]
    > 更新トークンが提供されるスコープは [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access) です。 それがどのように使用されるかについては、「[チュートリアル: Azure App Service でユーザーをエンド ツー エンドで認証および承認する](tutorial-auth-aad.md)」を参照してください。 他のスコープは、既に App Service によって既定で要求されます。 これらの既定のスコープについては、「[OpenID Connect のスコープ](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes)」を参照してください。

プロバイダーが構成されたら、トークン ストアで[更新トークンを見つけ、そのアクセス トークンの有効期限を確認](#retrieve-tokens-in-app-code)できます。 

任意の時点でアクセス トークンを更新するには、任意の言語で `/.auth/refresh` を呼び出します。 次のスニペットでは、jQuery を使用して、JavaScript クライアントからアクセス トークンを更新します。

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

ユーザーがアプリに許可されている権限を取り消すと、`/.auth/me` の呼び出しは `403 Forbidden` 応答で失敗する可能性があります。 エラーを診断するには、アプリケーション ログで詳細を確認します。

## <a name="extend-session-token-expiration-grace-period"></a>セッション トークンの有効期限の猶予期間の延長

認証されたセッションは、8 時間後に期限切れになります。 認証されたセッションの期限が切れた後、既定で 72 時間の猶予期間があります。 この猶予期間内は、ユーザーを再認証せずにセッション トークンを App Service で更新できます。 セッション トークンが無効になったときに `/.auth/refresh` を呼び出すことができ、トークンの有効期限を自分で追跡する必要はありません。 72 時間の猶予期間が経過した後、ユーザーはもう一度サインインして有効なセッション トークンを取得する必要があります。

72 時間が十分な時間でない場合は、この有効期間を延長することができます。 有効期限を長期に延長すると、セキュリティに大きく影響する可能性があります (認証トークンが漏洩または盗難にあった場合など)。 したがって、既定の 72 時間のままにするか、延長期間を最小限の値に設定する必要があります。

既定の有効期間を延長するには、[Cloud Shell](../cloud-shell/overview.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 猶予期間は、ID プロバイダーからのトークンではなく、App Service で認証されたセッションにのみ適用されます。 有効期限が切れたプロバイダー トークンの猶予期間はありません。 
>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
