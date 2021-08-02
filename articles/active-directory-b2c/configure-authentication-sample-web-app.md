---
title: Azure Active Directory B2C を使用してサンプル Web アプリケーションで認証を構成する
description: ASP.NET Web アプリケーションで Azure Active Directory B2C を使用してユーザーをサインインおよびサインアップします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 23b66bef9de9fc83884f882eee3ad21aba695b48
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071339"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用してサンプル Web アプリケーションで認証を構成する

この記事では、サンプルの ASP.NET Web アプリケーションを使用して、Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

> [!IMPORTANT]
> この記事で参照されているサンプルの ASP.NET Web アプリケーションは、アクセス トークンではなく ID トークンを返すため、REST API の呼び出しには使用できません。 REST API を呼び出すことができる Web アプリケーションについては、[Azure AD B2C を使用した ASP.NET Core でビルドされた WEB API のセキュリティ保護](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)に関するページを参照してください。  

## <a name="overview"></a>概要

OpenID Connect (OIDC) は OAuth 2.0 を基盤とした認証プロトコルであり、ユーザーをアプリケーションに安全にサインインさせるために利用できます。 この Web アプリのサンプルでは、[Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web) が使用されます。 Microsoft Identity Web は、Web アプリへの認証および承認のサポートの追加を簡略化する ASP.NET Core ライブラリのセットです。 

サインイン フローには、次の手順が含まれます。

1. ユーザーが Web アプリに移動し、 **[サインイン]** を選択します。 
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)、[パスワードのリセット](add-password-reset-policy.md)、または[ソーシャル アカウント](add-identity-provider.md)を使用したサインインを行います。
1. サインインに成功すると、Azure AD B2C によって ID トークンがアプリに返されます。
1. アプリによって、この ID トークンが検証され、要求が読み取られ、セキュリティで保護されたページがユーザーに返されます。

ID トークンの有効期限が切れた場合、またはアプリ セッションが無効になった場合、アプリによって新しい認証要求が開始され、ユーザーは Azure AD B2C にリダイレクトされます。 Azure AD B2C [SSO セッション](session-behavior.md)がアクティブになっている場合、ユーザーは再度サインインするように求められることなく、Azure AD B2C によってアクセス トークンが発行されます。 Azure AD B2C セッションが有効期限切れまたは無効になった場合、ユーザーは再度サインインするように求められます。

### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>前提条件

次のいずれかを実行しているコンピューター: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code (最新バージョン)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>手順 2: Web アプリケーションを登録する

アプリケーションが Azure AD B2C でサインインできるようにするには、Azure AD B2C ディレクトリにアプリを登録します。 アプリを登録すると、アプリと Azure AD B2C の間に信頼関係が確立されます。  

アプリの登録時に、**リダイレクト URI** を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証された後に Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、アプリを一意に識別する **アプリケーション ID** (**クライアント ID** とも呼ばれます) が生成されます。 アプリが登録されると、アプリケーション ID とリダイレクト URI の両方を使用して Azure AD B2C によって認証要求が作成されます。 

### <a name="register-the-app"></a>アプリを登録する

アプリ登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*webapp1* とします。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Web]** を選択し、URL テキスト ボックスに「`https://localhost:5001/signin-oidc`」と入力します。
1. **[アクセス許可]** で、 **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後の手順で Web アプリケーションを構成するときに使用します。

    ![アプリケーション ID を取得する](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="enable-id-tokens"></a>ID トークンを有効にする

Azure AD B2C から直接 ID トークンを要求する Web アプリの場合、アプリの登録で暗黙的な許可フローを有効にします。

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
1. **[暗黙的な許可]** で、 **[ID トークン]** チェック ボックスをオンにします。
1. **[保存]** を選択します。

## <a name="step-3-get-the-web-app-sample"></a>手順 3: Web アプリのサンプルを取得する

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)するか、GitHub からサンプル Web アプリケーションをクローンします。 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

パスの合計文字長が 260 未満のフォルダーにサンプル ファイルを展開します。

## <a name="step-4-configure-the-sample-application"></a>手順 4: サンプル アプリケーションを構成する

サンプル フォルダー内の `1-WebApp-OIDC/1-5-B2C/` フォルダーの下で、Visual Studio または Visual Studio Code を使用して **WebApp-OpenIDConnect-DotNet.csproj** プロジェクトを開きます。 

プロジェクト ルート フォルダーで、`appsettings.json` ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 アプリ設定の次のプロパティを更新します。 

|Section  |キー  |値  |
|---------|---------|---------|
|AzureAdB2C|インスタンス| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分。 たとえば、「 `https://contoso.b2clogin.com` 」のように入力します。|
|AzureAdB2C|Domain| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name)。 たとえば、「 `contoso.onmicrosoft.com` 」のように入力します。|
|AzureAdB2C|ClientId| [手順 2](#step-2-register-a-web-application) の Web API アプリケーション ID。|
|AzureAdB2C|SignUpSignInPolicyId|ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|

最終的な構成ファイルは、次の JSON のようになります。

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-application"></a>手順 5: サンプル アプリケーションを実行する

1. プロジェクトをビルドして実行します。
1. [http://.azurewebsites.net/admin](https://localhost:5001) を参照します。 
1. **[Sign Up/In]\(サインアップ/サインイン\)** を選択します。

    ![サインインまたはサインアップの選択](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. サインアップまたはサインイン プロセスを完了します。

認証が成功すると、ナビゲーション バーに表示名が表示されます。 Azure AD B2C トークンによってアプリに返される要求を表示するには、 **[Claims]\(要求\)** を選択します。

![Web アプリ トークンの要求](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする 

実稼働アプリケーションでは、アプリ登録リダイレクト URI は通常、アプリが実行されている公開されアクセス可能なエンドポイント (`https://contoso.com/signin-oidc` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ

* [コード サンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)についてさらに学習する
* [Azure AD B2C を使用して独自の Web アプリケーションで認証を有効にする](enable-authentication-web-application.md)方法について学習する