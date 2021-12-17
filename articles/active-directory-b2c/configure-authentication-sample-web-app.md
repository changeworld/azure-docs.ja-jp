---
title: Azure Active Directory B2C を使ってサンプル web アプリケーションで認証を構成する
description: この記事では、Azure Active Directory B2C を使用して ASP.NET web アプリケーションでサインインしたりユーザーをサインアップしたりする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 60f3383c58ab297fd5c2199d532591b1d3a38b8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572494"
---
# <a name="configure-authentication-in-a-sample-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使ってサンプル web アプリケーションで認証を構成する

この記事では、サンプルの ASP.NET Web アプリケーションを使用して、Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

> [!IMPORTANT]
> この記事で参照されているサンプルの ASP.NET web アプリケーションは、アクセス トークンではなく ID トークンを返すため、REST API の呼び出しのために使うことはできません。 REST API を呼び出すことができる web アプリについては、[ASP.NET Core でビルドされた Web API を、Azure AD B2C を使ってセキュリティで保護する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)をご覧ください。  

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 OIDC を使用して、ユーザーを安全にアプリケーションにサインインさせることができます。 この Web アプリのサンプルでは、[Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web) が使用されます。 Microsoft Identity Web は、web アプリへの認証および認可のサポートの追加を簡略化する ASP.NET Core ライブラリのセットです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーが Web アプリにアクセスして **[サインイン]** を選択します。 
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)し、[パスワードをリセット](add-password-reset-policy.md)します。 [ソーシャル アカウント](add-identity-provider.md)を使用してサインインすることもできます。
1. ユーザーがサインインに成功すると、Azure AD B2C はアプリに ID トークンを返します。
1. アプリは、ID トークンを認証し、要求の内容を読み取り、セキュリティで保護されたページをユーザーに返します。

ID トークンが有効期限切れになった場合、またはアプリ セッションが無効になった場合、アプリは新しい認証要求を開始し、ユーザーを Azure AD B2C にリダイレクトします。 Azure AD B2C [SSO セッション](session-behavior.md)がアクティブである場合、Azure AD B2C は、ユーザーに再度サインインを促さずにアクセス トークンを発行します。 Azure AD B2C セッションが有効期限切れまたは無効になった場合、ユーザーは再度サインインするように促されます。

### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>前提条件

次のいずれかを実行中のコンピューター: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* ASP.NET および web 開発ワークロードを用いる [Visual Studio の 2019 16.8 以降のバージョン](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
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

アプリの登録時に、*リダイレクト URI* を明記します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証を行った後、Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリ登録プロセスは、アプリを一意に識別する *アプリケーション ID* (または *クライアント ID* として知られています) を生成します。 アプリが登録されると、Azure AD B2C はアプリケーション ID とリダイレクト URI の両方を使用して認証要求を作成します。 

### <a name="step-21-register-the-app"></a>手順 2.1: アプリを登録する

Web アプリの登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** で、アプリケーションの名前を入力します (*webapp1* など)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Web]** を選択し、URL ボックスに「`https://localhost:5001/signin-oidc`」と入力します。
1. **[アクセス許可]** で、 **[Grant admin consent to openid and offline access permissions]\(OpenID とオフラインのアクセス許可に管理者の同意を与える\)** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web アプリケーション ID を記録するための Web アプリの [概要] ページのスクリーンショット。](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-enable-id-tokens"></a>手順 2.2: ID トークンを有効にする

Azure AD B2C から直接 ID トークンを要求する Web アプリの場合、アプリの登録で暗黙的な許可フローを有効にします。

1. 左側のペインの **[管理]** で、 **[認証]** を選択します。
1. **[暗黙的な許可]** では、 **[ID トークン]** (暗黙的およびハイブリッド フローに使用) と **[アクセス トークン]** (暗黙的なフローに使用) のチェックボックスを選択します。
1. **[保存]** を選択します。

## <a name="step-3-get-the-web-app-sample"></a>手順 3: Web アプリのサンプルを取得する

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)するか、GitHub からサンプル Web アプリケーションをクローンします。 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

パスの合計文字長が 260 以下のフォルダーにサンプル ファイルを展開します。

## <a name="step-4-configure-the-sample-web-app"></a>手順 4: サンプル Web アプリを構成する

サンプル フォルダー内の *1-WebApp-OIDC/1-5-B2C/* フォルダーで、Visual Studio または Visual Studio Code を使用して **WebApp-OpenIDConnect-DotNet.csproj** プロジェクトを開きます。 

プロジェクトのルート フォルダーで、*appsettings.json* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 次のアプリ設定のプロパティを更新します。 

|Section  |キー  |値  |
|---------|---------|---------|
|AzureAdB2C|インスタンス| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `https://contoso.b2clogin.com`)。|
|AzureAdB2C|Domain| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。|
|AzureAdB2C|ClientId| [手順 2](#step-2-register-a-web-application) の Web API アプリケーション ID。|
|AzureAdB2C|SignUpSignInPolicyId|ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|

最終的な構成ファイルは、次の JSON のようになります。

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-web-app"></a>手順 5: サンプル Web アプリを実行する

1. プロジェクトをビルドして実行します。
1. `https://localhost:5001` にアクセスします。 
1. **[サインアップ/イン]** を選択します。

    ![プロジェクトの Welcome ページの [サインアップ/イン] ボタンのスクリーンショット。](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. サインインアップまたはサインイン プロセスを完了します。

認証が成功すると、ナビゲーション バーに表示名が表示されます。 Azure AD B2C トークンによってアプリに返される要求を表示するには、 **[要求]** を選択します。

![Web アプリ トークン クレームのスクリーンショット。](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする 

運用アプリケーションでは、アプリ登録のリダイレクト URI は通常、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com/signin-oidc` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ

* [コード サンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)についてさらに確認する。
* [Azure AD B2C を使用して独自の Web アプリケーションで認証を有効にする](enable-authentication-web-application.md)方法について説明します。
