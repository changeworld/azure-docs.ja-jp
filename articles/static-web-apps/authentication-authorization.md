---
title: Azure Static Web Apps の認証と承認
description: さまざまな承認プロバイダーを使用して、静的アプリをセキュリティで保護する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: cshoe
ms.openlocfilehash: 8180dc98745079f351d321c971ed7d24d25b4b41
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002911"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Azure Static Web Apps の認証と承認

Azure Static Web Apps は、認証エクスペリエンスが効率化されています。 事前構成済みの一連のプロバイダーに既定でアクセスできるほか、必要に応じて[カスタム プロバイダーを登録](./authentication-custom.md)することもできます。

- すべてのユーザーは、有効なプロバイダーに対して認証を行うことができます。
- ユーザーはログイン後、既定では `anonymous` ロールと `authenticated` ロールに属します。
- 許可されているユーザーは、[staticwebapp.config.json ファイル](./configuration.md)に定義されたルールで、制限された[ルート](configuration.md#routes)にアクセスできます。
- ユーザーには、組込みの[招待](#invitations)システムを使用して、カスタム ロールが割り当てられます。
- API 関数を使用し、ログイン時にプログラムでユーザーにカスタム ロールを割り当てることができます。
- すべての認証プロバイダーは、既定で有効になっています。
  - 認証プロバイダーを制限するには、カスタム ルート規則によって[アクセスをブロック](#block-an-authorization-provider)します。
- 事前構成済みのプロバイダーは次のとおりです。
  - Azure Active Directory
  - GitHub
  - Twitter

認証と承認のサブジェクトは、ルーティングの概念とかなり共通点があります。ルーティングの概念については、[アプリケーション構成ガイド](configuration.md#routes)に詳しく説明されています。

## <a name="roles"></a>ロール

静的 Web アプリにアクセスするすべてのユーザーは、1 つまたは複数のロールに属しています。 ユーザーは、以下の 2 つの組み込みロールに属することができます。

- **匿名**:すべてのユーザーは自動的に "_匿名_" ロールに属します。
- **認証済み**:ログインしているすべてのユーザーは、"_認証済み_" ロールに属します。

組み込みロール以外にカスタム ロールをユーザーに割り当て、_staticwebapp.config.json_ ファイルで参照することができます。

## <a name="role-management"></a>ロール管理

# <a name="invitations"></a>[招待](#tab/invitations)

### <a name="add-a-user-to-a-role"></a>ユーザーをロールに追加する

ユーザーをロールに追加するには、ユーザーを特定のロールに関連付けることができる招待を生成します。 ロールは、_staticwebapp.config.json_ ファイル内で定義および管理されます。

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>招待を作成する

招待は個々の承認プロバイダーに固有であるため、サポートするプロバイダーを選択する際には、アプリのニーズを考慮してください。 プロバイダーによって、ユーザーの電子メール アドレスが公開される場合もあれば、サイトのユーザー名のみが提示される場合もあります。

<a name="provider-user-details" id="provider-user-details"></a>

| 承認プロバイダー | 公開されるユーザーの情報 |
| ---------------------- | ---------------- |
| Azure Active Directory | メール アドレス    |
| GitHub                 | username         |
| Twitter                | username         |

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. **[招待]** ボタンをクリックします。
1. オプションの一覧から _[承認プロバイダー]_ を選択します。
1. _[Invitee details]\(招待者の詳細\)_ ボックスに、受信者のユーザー名または電子メール アドレスを追加します。
   - GitHub および Twitter の場合は、ユーザー名を入力します。 それ以外の場合は、受信者の電子メール アドレスを入力します。
1. _[ドメイン]_ ドロップダウンから静的サイトのドメインを選択します。
   - 選択されたドメインは、招待に表示されるドメインになります。 サイトに関連付けられているカスタム ドメインがある場合は、そのカスタム ドメインを選択することができます。
1. _[ロール]_ ボックスにロール名のコンマ区切りの一覧を追加します。
1. 招待が有効なまま維持される最大時間数を入力します。
   - 指定できる最大上限は、168 時間 (7 日間) です。
1. **[生成]** ボタンをクリックします。
1. _[Invite link]\(招待リンク\)_ ボックスからリンクをコピーします。
1. アプリへのアクセス権を付与するユーザーに招待リンクを電子メールで送信します。

ユーザーが招待のリンクをクリックすると、該当のアカウントでログインするように求められます。 正常にログインが行われると、選択されたロールにユーザーが関連付けられます。

> [!CAUTION]
> ルート規則が、選択された認証プロバイダーと競合しないようにしてください。 ルート規則によってプロバイダーがブロックされると、ユーザーは招待を承諾することができなくなります。

### <a name="update-role-assignments"></a>ロールの割り当てを更新する

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. 一覧上でユーザーをクリックします。
1. _[ロール]_ ボックスで、ロールの一覧を編集します。
1. **[更新]** ボタンをクリックします。

### <a name="remove-user"></a>ユーザーの削除

1. [Azure portal](https://portal.azure.com) 上で Static Web Apps リソースに移動します。
1. _[設定]_ 下で、 **[ロール管理]** をクリックします。
1. 一覧上でユーザーを探します。
1. そのユーザーの行のチェック ボックスをオンにします。
1. **[削除]** ボタンをクリックします。

ユーザーを削除する際には、以下の点に注意してください。

1. ユーザーを削除すると、そのユーザーのアクセス許可は無効になります。
1. 世界規模での反映には、数分かかる場合があります。
1. ユーザーがアプリに再び追加される場合、[`userId` は変更されます](user-information.md)。

# <a name="function-preview"></a>[Function (preview)](#tab/function)

組み込みの招待システムを使用する代わりに、サーバーレス機能を使用し、ユーザーがログインするときにロールをプログラムでユーザーに割り当てることができます。

関数にカスタム ロールを割り当てるには、ユーザーが ID プロバイダーで正常に認証されるたびに自動的に呼び出される API 関数を定義します。 関数には、プロバイダーからユーザーの情報が渡されます。 関数は、ユーザーに割り当てられているカスタム ロールの一覧を返す必要があります。

この関数の使用例を次に示します。

- データベースに対してクエリを実行し、ユーザーに割り当てるロールを決定する
- [Microsoft Graph API](https://developer.microsoft.com/graph) を呼び出して、Active Directory グループ メンバーシップに基づいてユーザーのロール割を決定する
- ID プロバイダーによって返されたクレームに基づいてユーザーのロールを決定する

> [!NOTE]
> 関数を使用してロールを割り当てることができるのは、[カスタム認証](authentication-custom.md)が構成されている場合のみです。
>
> この機能を有効にすると、組み込みの招待システムを介して割り当てられたすべてのロールは無視されます。

### <a name="configure-a-function-for-assigning-roles"></a>ロールを割り当てる関数を構成する

ロールの割り当て機能として API 関数を使用するように Static Web Apps を構成するには、アプリケーションの[構成ファイル](configuration.md)のセクションに`rolesSource`プロパティ`auth`を追加します。 `rolesSource` プロパティの値は API 関数へのパスです。

```json
{
  "auth": {
    "rolesSource": "/api/GetRoles",
    "identityProviders": {
      // ...
    }
  }
}
```

> [!NOTE]
> 構成が完了すると、外部の HTTP 要求からロール割り当て関数にアクセスできなくなります。

### <a name="create-a-function-for-assigning-roles"></a>ロールを割り当てる関数を作成する

アプリの構成で `rolesSource` プロパティを定義した後で、指定したパスの静的 web アプリに [API 関数](apis.md)を追加します。 マネージド関数アプリを使用することも、独自の関数アプリを取り入れることもできます。

ユーザーが ID プロバイダーで正常に認証されるたびに、指定した関数が呼び出されます。 プロバイダーからのユーザー情報を含む JSON オブジェクトが要求本文で関数に渡されます。 ID プロバイダーによっては、関数でユーザー ID を使用して API 呼び出しを行うために使用できる `accessToken` もユーザー情報に含まれている場合があります。

Azure Active Directory からのペイロードの例を次に示します。

```json
{
  "identityProvider": "aad",
  "userId": "72137ad3-ae00-42b5-8d54-aacb38576d76",
  "userDetails": "ellen@contoso.com",
  "claims": [
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname",
          "val": "Contoso"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname",
          "val": "Ellen"
      },
      {
          "typ": "name",
          "val": "Ellen Contoso"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/objectidentifier",
          "val": "7da753ff-1c8e-4b5e-affe-d89e5a57fe2f"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
          "val": "72137ad3-ae00-42b5-8d54-aacb38576d76"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/tenantid",
          "val": "3856f5f5-4bae-464a-9044-b72dc2dcde26"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "ver",
          "val": "1.0"
      }
  ],
  "accessToken": "eyJ0eXAiOiJKV..."
}
```

関数は、ユーザーの情報を使用して、ユーザーに割り当てるロールを決定できます。 関数は、ユーザーに割り当てるカスタム ロール名のリストを含む JSON 本文を含む HTTP 200 応答を返す必要があります。

たとえば、ユーザーに `Reader` と `Contributor` のロールを割り当てるには、次の応答を返します。

```json
{
  "roles": [
    "Reader",
    "Contributor"
  ]
}
```

ユーザーに追加のロールを割り当てたくない場合は、空の `roles` 配列を返します。

詳細については、「[チュートリアル: 関数と Microsoft Graph を使用してカスタム ロールを割り当てる](assign-roles-microsoft-graph.md)」を参照してください。

---

## <a name="remove-personal-identifying-information"></a>個人が特定される情報を削除する

エンド ユーザーとしてアプリケーションに同意すると、ID プロバイダーに応じてメール アドレスまたはユーザー名に、そのアプリケーションからアクセスできるようになります。 この情報が提供されたら、アプリケーションの所有者は、個人が特定される情報をどのように管理するかを決定します。

この情報をシステムから取り消すには、エンド ユーザーは、個々の Web アプリの管理者に連絡する必要があります。

Azure Static Web Apps プラットフォームから個人が特定される情報を削除し、今後の要求でプラットフォームからこの情報が提供されないようにするには、以下の URL を使用して要求を送信します。

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

今後の要求でプラットフォームから個々のアプリに対してこの情報が提供されないようにするには、以下の URL へ要求を送信します。

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Azure Active Directory を使用する場合は、`<AUTHENTICATION_PROVIDER_NAME>` プレースホルダーの値として `aad` を使用することに注意してください。

## <a name="system-folder"></a>システム フォルダー

Azure Static Web Apps では、`/.auth` システム フォルダーを使用して、承認関連の API へのアクセスが提供されています。 `/.auth` フォルダー下のいずれかのルートをエンドユーザーに直接公開するのではなく、[ルーティング規則](configuration.md#routes)を作成してわかりやすい URL を作ることを検討してください。

## <a name="login"></a>ログイン

次の表を使用して、プロバイダー固有のルートを確認します。

| 承認プロバイダー | ログイン ルート             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| GitHub                 | `/.auth/login/github`   |
| Twitter                | `/.auth/login/twitter`  |

たとえば、GitHub にログインするために、次のスニペットのようなリンクを含めることができます。

```html
<a href="/.auth/login/github">Login</a>
```

複数のプロバイダーをサポートすることを選択した場合は、それぞれに対応するプロバイダー固有のリンクを Web サイト上に公開する必要があります。

[ルート規則](./configuration.md#routes)を使用して、既定のプロバイダーを _/login_ のようなわかりやすいルートにマップできます。

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>ログイン後のリダイレクト

ログイン後にユーザーが特定のページに戻るようにするには、`post_login_redirect_uri` クエリ文字列パラメーターに完全修飾 URL を指定します。

次に例を示します。

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Logout

`/.auth/logout` ルートでは、Web サイトからユーザーをログアウトします。 次の例に示すように、サイト ナビゲーションにユーザーがログアウトできるリンクを追加することが可能です。

```html
<a href="/.auth/logout">Log out</a>
```

[ルート規則](./configuration.md#routes)を使用して、 _/login_ のようなわかりやすいルートにマップできます。

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>ログアウト後のリダイレクト

ログアウト後にユーザーが特定のページに戻るようにするには、`post_logout_redirect_uri` クエリ文字列パラメーターに URL を指定します。

## <a name="block-an-authorization-provider"></a>承認プロバイダーをブロックする

承認プロバイダーを使用しないように、アプリを制限することができます。 たとえば、お使いのアプリ上で、[電子メール アドレスを公開するプロバイダー](#provider-user-details)のみに統一したい場合があります。

プロバイダーをブロックするには、[ルート規則](configuration.md#routes)を作成して、ブロックされたプロバイダー固有のルートへの要求に 404 を返すことができます。 たとえば、プロバイダーとして Twitter を制限するには、次のルート規則を追加します。

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": 404
}
```

## <a name="restrictions"></a>制限

一般的な制限事項と限度については、[クォータに関する記事](quotas.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ユーザーの認証と承認のデータにアクセスする](user-information.md)

<sup>1</sup> 外部証明書は保留されています。
