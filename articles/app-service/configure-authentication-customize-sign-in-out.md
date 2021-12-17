---
title: サインインとサインアウトのカスタマイズ
description: App Service で組み込みの認証と認可を使用し、同時に、サインインとサインアウトの動作をカスタマイズします。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: bdb100afa821aa08fb831aac53b1eb80cdda043e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429543"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>Azure App Service 認証でのサインインとサインアウトのカスタマイズ

この記事では、組み込みの[認証と認可を App Service で](overview-authentication-authorization.md)使用しているときにユーザーのサインインとサインアウトをカスタマイズする方法について説明します。 

## <a name="use-multiple-sign-in-providers"></a>複数のサインイン プロバイダーを使用する

ポータル構成では、ユーザーに複数 (Facebook と Twitter の両方など) のサインイン プロバイダーを表示するターンキー手法は提供されません。 ただし、この機能をアプリに追加することは難しくありません。 手順の概要は次のとおりです。

最初に、Azure Portal の **[認証/承認]** ページで、有効にする各 ID プロバイダーを構成します。

**[要求が認証されない場合に実行するアクション]** で、 **[匿名要求を許可する (操作不要)]** を選択します。

サインイン ページ、ナビゲーション バー、またはアプリのその他の任意の場所で、有効にした各プロバイダーへのサインイン リンク (`/.auth/login/<provider>`) を追加します。 次に例を示します。

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

ユーザーがいずれかのリンクをクリックすると、それぞれのサインイン ページが開き、ユーザーがサインインできます。

サインイン後のユーザーをカスタム URL にリダイレクトさせるには、`post_login_redirect_uri` クエリ文字列パラメーターを使用します (ご利用の ID プロバイダーの構成におけるリダイレクト URI と混同しないでください)。 たとえば、サインイン後にユーザーを `/Home/Index` にリダイレクトさせるには、次の HTML コードを使用します。

```html
<a href="/.auth/login/<provider>?post_login_redirect_uri=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>クライアント主導型サインイン

クライアント主導型サインインでは、アプリケーションでは、プロバイダー固有の SDK を使用して ID プロバイダーにユーザーがサインインします。 その後、結果的に生成された認証トークンが HTTP POST 要求を利用してアプリケーション コードによって App Service に検証のために送信されます (「[認証フロー](overview-authentication-authorization.md#authentication-flow)」参照)。 [Azure Mobile Apps SDK](https://github.com/Azure/azure-mobile-apps) ではこのサインイン フローが使用されます。 この検証自体では、必要なアプリ リソースへのアクセス権が実際には付与されませんが、検証に成功すると、アプリ リソースへのアクセスに使用できるセッション トークンが付与されます。

プロバイダーのトークンを検証するには、最初に目的のプロバイダーを使用して App Service のアプリが構成されている必要があります。 実行時に、プロバイダーから認証トークンを取得した後、検証のためにトークンを `/.auth/login/<provider>` にポストします。 次に例を示します。

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

トークンの形式は、プロバイダーによって若干異なります。 詳しくは、以下の表をご覧ください。

| プロバイダーの値 | 要求本文に必要 | 説明 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | `id_token`、`refresh_token`、`expires_in` プロパティは省略可能です。 |
| `microsoftaccount` | `{"access_token":"<access_token>"}` または `{"authentication_token": "<token>"`| `authentication_token` は `access_token` よりも優先されます。 `expires_in` プロパティは省略可能です。 <br/> ライブ サービスからトークンを要求する場合は、常に `wl.basic` スコープを要求します。 |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` プロパティは省略可能です。 `authorization_code` 値を指定すると、アクセス トークンと更新トークンがトークン ストアに追加されます。 指定した場合、`authorization_code` には必要に応じて `redirect_uri` プロパティを指定することもできます。 |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook からの有効な[ユーザー アクセス トークン](https://developers.facebook.com/docs/facebook-login/access-tokens)を使用します。 |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

プロバイダー トークンが正常に検証された場合、API は、セッション トークンである `authenticationToken` を応答本文に入れて戻ります。 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

このセッション トークンを入手したら、`X-ZUMO-AUTH` ヘッダーを HTTP 要求に追加することで、保護対象のアプリ リソースにアクセスすることができます。 次に例を示します。 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>セッションからサインアウトする

ユーザーは、アプリの `/.auth/logout` エンドポイントに `GET` 要求を送信することでサインアウトを開始できます。 `GET` 要求は次の処理を行います。

- 現在のセッションから認証 Cookie をクリアします。
- トークン ストアから現在のユーザーのトークンを削除します。
- Azure Active Directory と Google の場合、ID プロバイダーでサーバー側のサインアウトを実行します。

Web ページの簡単なサインアウト リンクを次に示します。

```html
<a href="/.auth/logout">Sign out</a>
```

既定では、サインアウトに成功すると、クライアントは URL `/.auth/logout/done` にリダイレクトされます。 `post_logout_redirect_uri` クエリ パラメーターを追加して、サインアウト後のリダイレクト ページを変更できます。 次に例を示します。

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri` の値を[エンコード](https://wikipedia.org/wiki/Percent-encoding)することをお勧めします。

完全修飾 URL を使用している場合、URL は同じドメインでホストされているか、アプリの許可された外部リダイレクト URL として構成されている必要があります。 次の例では、同じドメインにホストされていない `https://myexternalurl.com` にリダイレクトします。

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL フラグメントを保持する

ユーザーは、アプリにサインインすると、通常は `/wiki/Main_Page#SectionZ` のように同じページの同じセクションにリダイレクトされたいと考えます。 ただし、[URL フラグメント](https://wikipedia.org/wiki/Fragment_identifier) (たとえば `#SectionZ`) はサーバーに送信されないため、OAuth のサインインが完了してアプリにリダイレクトされた後は既定で保持されません。 目的のアンカーに再度移動する必要がある場合、ユーザー エクスペリエンスは最適ではありません。 この制限は、すべてのサーバー側認証ソリューションに適用されます。

App Service 認証では、OAuth サインイン全体で URL フラグメントを保存できます。 これを行うには、`WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` というアプリ設定を `true` に設定します。 [Azure portal](https://portal.azure.com) で行うか、[Azure Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行するだけです。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="limit-the-domain-of-sign-in-accounts"></a>サインイン アカウントのドメインの制限

Microsoft アカウントと Azure Active Directory の両方に複数のドメインからサインインできます。 たとえば、Microsoft アカウントでは _outlook.com_、_live.com_、_hotmail.com_ アカウントが許可されます。 Azure AD では、サインイン アカウントに任意の数のカスタム ドメインが許可されます。 ただし、ユーザーを独自のブランドの Azure AD サインインページ (`contoso.com`など) に直接誘導することもできます。 サインイン アカウントのドメイン名を提示するには、以下の手順に従います。

[https://resources.azure.com](https://resources.azure.com) で、**subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings** に移動します。 

**[Edit]** をクリックし、次のプロパティを変更し、 **[Put]** をクリックします。 _\<domain\_name>_ は使用するドメインで置き換えてください。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

この設定で、`domain_hint` クエリ文字列パラメーターがログイン リダイレクト URL に追加されます。 

> [!IMPORTANT]
> クライアントは、リダイレクト URL を受け取った後で `domain_hint` パラメーターを削除し、別のドメインでログインできます。 そのため、この機能は便利ですが、セキュリティ機能ではありません。
>

## <a name="authorize-or-deny-users"></a>ユーザーを承認または拒否する

App Service は最も単純な承認ケース (つまり、認証されていない要求の拒否) を処理しますが、アプリでは、特定のユーザー グループだけにアクセスを制限するなど、よりきめ細かな承認動作が必要になる場合があります。 場合によっては、サインインしたユーザーに対してアクセスを許可または拒否するためのカスタム アプリケーション コードの記述が必要になります。 また、App Service やお使いの ID プロバイダーが、コードの変更を必要とせずにサポートできる場合もあります。

- [サーバー レベル](#server-level-windows-apps-only)
- [ID プロバイダー レベル](#identity-provider-level)
- [アプリケーション レベル](#application-level)

### <a name="server-level-windows-apps-only"></a>サーバー レベル (Windows アプリのみ)

Windows アプリでは、*Web.config* ファイルを編集して IIS Web サーバーの承認動作を定義できます。 Linux アプリは、IIS を使用しないため、*Web.config* を使用して構成することはできません。

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動します

1. App Service ファイルのブラウザー エクスプローラーで、*site/wwwroot* に移動します。 *Web.config* が存在しない場合は、 **+**  >  **[新しいファイル]** を選択して作成します。 

1. *Web.config* の鉛筆を選択して編集します。 次の構成コードを追加し、 **[保存]** をクリックします。 *Web.config* が既に存在する場合は、すべての内容を含めた `<authorization>` 要素を追加するだけです。 許可するアカウントを `<allow>` 要素に追加します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>ID プロバイダー レベル

ID プロバイダーによって特定のターンキー承認が提供される場合があります。 次に例を示します。

- [Azure App Service](configure-authentication-provider-aad.md) の場合、Azure AD で直接、[エンタープライズ レベルのアクセスを管理](../active-directory/manage-apps/what-is-access-management.md)できます。 説明については、「[アプリケーションへのユーザー アクセスの削除方法](../active-directory/manage-apps/methods-for-removing-user-access.md)」をご覧ください。
- [Google](configure-authentication-provider-google.md) の場合、特定の [組織](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)に属する Google API プロジェクトを、その組織内のユーザーだけにアクセスを許可するように構成できます ([Google の「**Setting up OAuth 2.0**」サポート ページ](https://support.google.com/cloud/answer/6158849?hl=en)をご覧ください)。

### <a name="application-level"></a>アプリケーション レベル

他のいずれのレベルでも必要な承認が提供されない場合、またはお使いのプラットフォームまたは ID プロバイダーがサポートされていない場合、[ユーザーの要求](configure-authentication-user-identities.md)に基づいてユーザーを承認するカスタム コードを記述する必要があります。

## <a name="more-resources"></a>その他のリソース

- [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
- [認証用の環境変数とアプリ設定](reference-app-settings.md#authentication--authorization)
