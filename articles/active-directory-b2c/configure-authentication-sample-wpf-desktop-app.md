---
title: Azure Active Directory B2C を使用してサンプル WPF デスクトップ アプリケーションで認証を構成する
description: この記事では、Azure Active Directory B2C を使用して WPF デスクトップ アプリケーションでユーザーをサインインおよびサインアップさせる方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d10fd1586ef60bb0c216e8284fa7e6bf44804217
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007609"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使ってサンプル WPF デスクトップ アプリで認証を構成する

この記事では、サンプルの [Windows Presentation Foundation (WPF) デスクトップ](/visualstudio/designers/getting-started-with-wpf) アプリケーションを使用して、Azure Active Directory B2C (Azure AD B2C) 認証をデスクトップ アプリに追加する方法について説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 OIDC を使用して、ユーザーを安全にアプリケーションにサインインさせることができます。 このデスクトップ アプリのサンプルでは、OIDC 認可コード Proof Key for Code Exchange (PKCE) フローで [Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md) を使用します。 MSAL は、デスクトップ アプリへの認証と認可サポートの追加を簡略化する、Microsoft 提供のライブラリです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーがアプリを開き、**[サインイン]** を選択します。
1. アプリによってデスクトップ デバイスのシステム ブラウザーが開き、Azure AD B2C に対する認証要求が開始されます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)、[パスワードのリセット](add-password-reset-policy.md)、または[ソーシャル アカウント](add-identity-provider.md)を使用したサインインを行います。
1. ユーザーがサインインに成功すると、Azure AD B2C からアプリに認可コードが返されます。
1. アプリによって次のアクションが実行されます。
    1. 認可コードが、ID トークン、アクセス トークン、更新トークンと交換されます。
    1. ID トークン要求が読み取られます。
    1. 後で使用できるように、トークンがメモリ内キャッシュに格納されます。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- **デスクトップ アプリケーション** を登録すると、アプリは Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、*リダイレクト URI* を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証を行った後、Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、デスクトップ アプリを一意に識別する "アプリケーション ID″ ("クライアント ID″ とも呼ばれる) が生成されます (たとえば "アプリ ID: 1″)。

- **Web API** を登録すると、アプリは保護された Web API を呼び出せるようになります。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別する "アプリケーション ID″ が生成されます (たとえば "アプリ ID: 2″)。 デスクトップ アプリ (App ID: 1) のアクセス許可を Web API スコープ (App ID: 2) に付与します。 

アプリケーションの登録とアーキテクチャについて、次の図に示します。

![Web API、登録、トークンを含むデスクトップ アプリの図。](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>サインアウト フロー

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

.NET デスクトップ開発で [Visual Studio 2019](https://www.visualstudio.com/downloads/) を実行しているコンピューター。

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>手順 2: アプリケーションを登録する

デスクトップ アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="step-21-register-the-web-api-app"></a>手順 2.1: Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>手順 2.2: Web API アプリのスコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-desktop-app"></a>手順 2.3: デスクトップ アプリを登録する

デスクトップ アプリの登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** には、アプリケーションの名前を入力します (たとえば *desktop-app1*)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、**[パブリック クライアント/ネイティブ (デスクトップとデスクトップ)]** を選択して、URL ボックスに「`https://your-tenant-name.b2clogin.com/oauth2/nativeclient`」と入力します。 `your-tenant-name` を、実際の [テナント名](tenant-management.md#get-your-tenant-name) に置き換えます。 その他のオプションについては、「[リダイレクト URI を構成する](enable-authentication-wpf-desktop-app-options.md#configure-the-redirect-uri)」を参照してください。
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. 後にデスクトップ アプリケーションを構成するときに使うために、**アプリケーション (クライアント) ID** を記録しておきます。
    ![デスクトップ アプリケーション ID が強調表示されたスクリーンショット。](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-grant-the-desktop-app-permissions-for-the-web-api"></a>手順 2.4: デスクトップ アプリに Web API に対するアクセス許可を付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>手順 3: サンプル Web API を構成する

このサンプルでは、デスクトップ アプリが Web API に対して使用できる、関連するスコープを持つアクセス トークンを取得します。  コードから Web API を呼び出すには、次の手順を実行します。

1. 既存の Web API を使用するか、新しいものを作成します。 詳細については、「[Azure AD B2C を使用した独自の WEB API での認証の有効化](enable-authentication-web-api.md)」を参照してください。
1. Web API を構成したら、その Web API エンドポイントの URI をコピーします。 次の手順でその Web API エンドポイントを使用します。

> [!TIP]
> Web API がなくても、このサンプルを実行できます。 この場合、アプリはアクセス トークンを返しますが、Web API を呼び出すことはできません。 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>手順 4: WPF デスクトップ アプリのサンプルを取得する

1. [.zip ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)するか、[GitHub リポジトリ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)からサンプル Web アプリケーションをクローンします。 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. Visual Studio で、**active-directory-b2c-wpf** ソリューション (*active-directory-b2c-wpf.sln* ファイル) を開きます。



## <a name="step-5-configure-the-sample-desktop-app"></a>手順 5: サンプル デスクトップ アプリを構成する

**active-directory-b2c-wpf** プロジェクトで、*App.xaml.cs* ファイルを開きます。 `App.xaml.cs` クラス メンバーに、使用している Azure AD B2C ID プロバイダーに関する情報が含まれています。 デスクトップ アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 

次のクラス メンバーを更新します。

|キー  |値  |
|---------|---------|
|`TenantName`|Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `contoso.b2clogin.com`)。|
|`ClientId`|[手順 2.3](#step-23-register-the-desktop-app) のデスクトップ アプリケーション ID。|
|`PolicySignUpSignIn`| [手順 1](#step-1-configure-your-user-flow) で作成したサインアップまたはサインイン ユーザー フローあるいはカスタム ポリシー。|
|`PolicyEditProfile`|[手順 1](#step-1-configure-your-user-flow) で作成したプロファイル編集ユーザー フローまたはカスタム ポリシー。|
|`ApiEndpoint`| (省略可能) [手順 3](#step-3-configure-the-sample-web-api) で作成した Web API エンドポイント (例: `https://contoso.azurewebsites.net/hello`)。|
| `ApiScopes` | [手順 2.4](#step-24-grant-the-desktop-app-permissions-for-the-web-api) で作成した Web API のスコープ。|
| | | 

最終的な *App.xaml.cs* ファイルは、次の C# コードのようになります。

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>手順 6: デスクトップ アプリを実行してテストする

1. [NuGet パッケージを復元します](/nuget/consume-packages/package-restore)。
1. F5 キーを選択し、サンプルをビルドして実行します。
1. **[サインイン]** を選択してから、Azure AD B2C のローカルまたはソーシャル アカウントを使用して、サインアップまたはサインインします。

    ![サインイン フローを開始する方法を示すスクリーンショット。](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. サインアップまたはサインインに成功すると、WPF アプリの下部ウィンドウにトークンの詳細が表示されます。

    ![Azure AD B2C アクセス トークンとユーザー ID を示すスクリーンショット。](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. **[API の呼び出し]** を選択して、Web API を呼び出します。


## <a name="next-steps"></a>次の手順

[Azure AD B2C を使用して WPF デスクトップ アプリの認証オプションを構成する](enable-authentication-wpf-desktop-app-options.md)方法をご覧ください。
