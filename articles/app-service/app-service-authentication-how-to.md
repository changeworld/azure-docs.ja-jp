---
title: Azure App Service での認証と承認のカスタマイズ | Microsoft Docs
description: App Service で認証と承認をカスタマイズし、ユーザーの要求とさまざまなトークンを取得する方法を示します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: 191d42f43e500c7f8041a02aeba2fbcb7dfd5379
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226528"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Azure App Service での認証と承認のカスタマイズ

この記事では、[App Service で認証と承認](app-service-authentication-overview.md)をカスタマイズする方法と、アプリケーションから ID を管理する方法について説明します。 

すぐに開始するには、以下のチュートリアルのいずれかをご覧ください。

* [チュートリアル: Azure App Service (Windows) でユーザーをエンド ツー エンドで認証および承認する](app-service-web-tutorial-auth-aad.md)
* [チュートリアル: Linux 用 Azure App Service でユーザーをエンド ツー エンドで認証および承認する](containers/tutorial-auth-aad.md)
* [Azure Active Directory ログインを使用するようにアプリを構成する方法](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook ログインを使用するようにアプリを構成する方法](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google ログインを使用するようにアプリを構成する方法](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft アカウント ログインを使用するようにアプリを構成する方法](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter ログインを使用するようにアプリを構成する方法](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>複数のサインイン オプションの構成

ポータル構成では、ユーザーに複数 (Facebook と Twitter の両方など) のサインイン オプションを表示するターンキー手法は提供されません。 ただし、Web アプリに機能を追加することは難しくはありません。 手順の概要は次のとおりです。

最初に、Azure Portal の **[認証/承認]** ページで、有効にする各 ID プロバイダーを構成します。

**[要求が認証されない場合に実行するアクション]** で、**[匿名要求を許可する (操作不要)]** を選択します。

サインイン ページ、ナビゲーション バーまたは Web アプリの他の任意の場所で、有効にする各プロバイダーへのサインイン リンクを追加します (`/.auth/login/<provider>`)。 例: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

ユーザーがいずれかのリンクをクリックすると、それぞれのサインイン ページが開き、ユーザーがサインインできます。

サインイン後のユーザーをカスタム URL にリダイレクトさせるには、`post_login_redirect_url` クエリ文字列パラメーターを使用します (ご利用の ID プロバイダーの構成におけるリダイレクト URI と混同しないでください)。 たとえば、サインイン後にユーザーを `/Home/Index` にリダイレクトさせるには、次の HTML コードを使用します。

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="access-user-claims"></a>ユーザー要求へのアクセス

App Service では、特殊なヘッダーを使用して、アプリケーションにユーザー要求を渡します。 外部要求ではこれらのヘッダーの設定が許可されないので、これらのヘッダーは App Service によって設定されている場合にのみ存在します。 いくつかのヘッダーの例は次のとおりです。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

任意の言語またはフレームワークで記述されたコードで、これらのヘッダーから必要な情報を取得できます。 ASP.NET 4.6 アプリの場合は、 **ClaimsPrincipal** が自動的に適切な値に設定されます。

アプリケーションでは、`/.auth/me` を呼び出して認証されたユーザーの追加の詳細を取得することもできます。 Mobile Apps サーバー SDK には、このデータを操作するためのヘルパー メソッドが用意されています。 詳細については、「[Azure Mobile Apps Node.js SDK の使用方法](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)」と「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)」を参照してください。

## <a name="retrieve-tokens-in-app-code"></a>アプリ コードでのトークンの取得

サーバー コードからプロバイダー固有のトークンが要求ヘッダーに挿入されるので、これらのトークンに簡単にアクセスできます。 次の表は、可能なトークン ヘッダー名を示しています。

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook トークン | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft アカウント | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

クライアント コード (モバイル アプリやブラウザー内の JavaScript など) から、HTTP `GET` 要求を `/.auth/me` に送信します。 返される JSON にはプロバイダー固有のトークンがあります。

> [!NOTE]
> アクセス トークンはプロバイダー リソースへのアクセス用であるため、クライアント シークレットを使用してプロバイダーを構成する場合にのみ存在します。 更新トークンの取得方法については、「[更新アクセス トークン](#refresh-access-tokens)」をご覧ください。

## <a name="refresh-access-tokens"></a>更新アクセス トークン

プロバイダーのアクセス トークンが期限切れになった場合は、ユーザーを再認証する必要があります。 アプリケーションの `/.auth/refresh` エンドポイントに `GET` 呼び出しを行って、トークンの期限切れを回避することができます。 呼び出されると、App Service は認証されたユーザーのトークン ストア内のアクセス トークンを自動的に更新します。 アプリ コードによる後続のトークン要求で、更新トークンを取得します。 ただし、トークンの更新が動作するためには、トークン ストアにプロバイダーの[更新トークン](https://auth0.com/learn/refresh-tokens/)が含まれている必要があります。 更新トークンの取得方法は各プロバイダーによって文書化されていますが、次の一覧に概要を示します。

- **Google**: `access_type=offline` クエリ文字列パラメーターを `/.auth/login/google` API 呼び出しに追加します。 Mobile Apps SDK を使用している場合は、`LogicAsync` オーバーロードの 1 つにパラメーターを追加できます ([Google 更新トークン](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)に関するページをご覧ください)。
- **Facebook**: 更新トークンを提供しません。 長期間維持されるトークンの有効期限は 60 日間です ([Facebook のアクセス トークンの有効期限と延長](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)に関するページをご覧ください)。
- **Twitter**: アクセス トークンに有効期限はありません ([Twitter OAuth の FAQ](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq) に関するページをご覧ください)。
- **Microsoft アカウント**: [Microsoft アカウント認証設定を構成する](app-service-mobile-how-to-configure-microsoft-authentication.md)場合は、`wl.offline_access` スコープを選択します。
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com) で、次の手順を実行します。
    1. ページの上部にある **[Read/Write]** を選択します。
    1. 左側のブラウザーで、**subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings** に移動します。 
    1. **[編集]** をクリックします。
    1. 次のプロパティを変更します。 _\<app\_id>_ を、アクセスするサービスの Azure Active Directory アプリケーション ID に置き換えます。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. **[Put]** をクリックします。 

プロバイダーが構成されたら、トークン ストアで[更新トークンを見つけ、そのアクセス トークンの有効期限を確認](#retrieve-tokens-in-app-code)できます。 

任意の時点でアクセス トークンを更新するには、任意の言語で `/.auth/refresh` を呼び出します。 次のスニペットでは、jQuery を使用して、JavaScript クライアントからアクセス トークンを更新します。

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

ユーザーがアプリに許可されている権限を取り消すと、`/.auth/me` の呼び出しは `403 Forbidden` 応答で失敗する可能性があります。 エラーを診断するには、アプリケーション ログで詳細を確認します。

## <a name="extend-session-expiration-grace-period"></a>セッションの有効期限の猶予期間の延長

認証されたセッションの期限が切れた後、既定で 72 時間の猶予期間があります。 この猶予期間内は、ユーザーを再認証せずにセッション Cookie またはセッション トークンを App Service で更新できます。 セッション Cookie またはセッション トークンが無効になったときに `/.auth/refresh` を呼び出すことができ、トークンの有効期限を自分で追跡する必要はありません。 72 時間の猶予期間が経過した後、ユーザーはもう一度サインインして有効なセッション Cookie またはセッション トークンを取得する必要があります。

72 時間が十分な時間でない場合は、この有効期間を延長することができます。 有効期限を長期に延長すると、セキュリティに大きく影響する可能性があります (認証トークンが漏洩または盗難にあった場合など)。 したがって、既定の 72 時間のままにするか、延長期間を最小限の値に設定する必要があります。

既定の有効期間を延長するには、[Cloud Shell](../cloud-shell/overview.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 猶予期間は、ID プロバイダーからのトークンではなく、App Service で認証されたセッションにのみ適用されます。 有効期限が切れたプロバイダー トークンの猶予期間はありません。 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>サインイン アカウントのドメインの制限

Microsoft アカウントと Azure Active Directory の両方に複数のドメインからサインインできます。 たとえば、Microsoft アカウントでは _outlook.com_、_live.com_、_hotmail.com_ アカウントが許可されます。 Azure Active Directory では、サインイン アカウントに任意の数のカスタム ドメインが許可されます。 この動作は、ユーザーが _outlook.com_ アカウントでアクセスすることが望ましくない内部アプリでは望ましくない場合があります。 サインイン アカウントのドメイン名を制限するには、以下の手順に従います。

[https://resources.azure.com](https://resources.azure.com) で、**subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings** に移動します。 

**[Edit]** をクリックし、次のプロパティを変更し、**[Put]** をクリックします。 _\<domain\_name>_ は使用するドメインで置き換えてください。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: エンドツーエンドでのユーザーの認証と承認 (Windows)](app-service-web-tutorial-auth-aad.md)
> [チュートリアル: エンドツーエンドでのユーザーの認証と承認 (Linux)](containers/tutorial-auth-aad.md)