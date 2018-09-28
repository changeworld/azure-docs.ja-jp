---
title: Azure AD v2.0 ASP.NET Core Web アプリのクイック スタート | Microsoft Docs
description: OpenID Connect を使用して、ASP.NET Core Web アプリで Microsoft サインインを実装する方法について説明します
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: f57dc1707a9e25e4ea308142e804cdbc80d4308a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984944"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリに Microsoft サインインを追加する

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

このクイック スタートには、ASP.NET Core Web アプリが個人アカウント (hotmail.com、live.com など) および職場または学校アカウントを任意の Azure Active Directory インスタンスからサインインさせることができる方法を示すサンプル コードが含まれています。

![このクイック スタートで生成されたサンプル アプリの動作](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>アプリケーションの登録とクイック スタート アプリのダウンロード
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>アプリケーションとサンプル コードの登録と構成
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
> 
> 1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動します。
> 1. アプリケーションの名前を入力し、**ガイド付きセットアップ**のオプションがオフになっていることを確認し、**[作成]** をクリックします。
> 1. `Add Platform` をクリックし、`Web` を選択します。
> 1. **[暗黙的フローを許可する]** が "*オンになっている*" ことを確認します。
> 1. **[リダイレクト URL]** に「`https://localhost:3110/`」と入力します。
> 1. ページの下部までスクロールし、**[保存]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
> このクイック スタートのサンプル コードを動作させるには、応答 URL として `http://localhost:3110/` を追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-aspnet-core-webapp/green-check.png): アプリケーションはこの属性で構成されます。

#### <a name="step-2-download-your-aspnet-core-project"></a>手順 2: ASP.NET Core プロジェクトのダウンロード

- [ASP.NET Core プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)します。

#### <a name="step-3-configure-your-project"></a>手順 3: プロジェクトの構成

1. ローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを解凍します。
1. Visual Studio 2017 を使用する場合は、Visual Studio でプロジェクトを開きます (任意)。
1. **appsettings.json** を編集し、`ClientId` の値を、登録したアプリケーションのアプリケーション ID に置き換えます。

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. アプリケーションが "*シングル テナント アプリケーション*" (現在のディレクトリ内のアカウントのみをターゲットにする) 場合は、**appsettings.json** ファイルで `TenantId` の値を探し、`common` を**テナント ID** または**テナント名** に置き換えます (例: contoso.microsoft.com)。 テナント名は **[概要] ページ**で取得できます。

## <a name="more-information"></a>詳細情報

このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 これは、コードの機能や主な引数について理解するために、また、既存の ASP.NET Core アプリケーションにサインインを追加する場合にも役立ちます。

### <a name="startup-class"></a>スタートアップ クラス

*Microsoft.AspNetCore.Authentication* ミドルウェアは、ホスト プロセスの初期化時に実行されるスタートアップ クラスを使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

`AddAuthentication` メソッドは、ブラウザーのシナリオで使用される Cookie ベースの認証を追加するようにサービスを構成し、OpenIdConnect へのチャレンジも設定します。 

`.AddAzureAd` を含む行によって、Azure Active Directory 認証がアプリケーションに追加されます。

さらに、**AzureAdAuthenticationBuilderExtensions.cs** ファイルによって、拡張メソッドが Azure AD 認証パイプラインに追加されます。 この拡張メソッドは、Azure AD Authentication に必要な属性を構成します。 `IConfigureNamedOptions` インターフェイスの `Configure` メソッドには、以下が含まれます。

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |ClientId     |Azure portal に登録されているアプリケーションのアプリケーション ID|
> |Authority | ユーザーを認証するための STS エンドポイント。 パブリック クラウドでは、通常は https://login.microsoftonline.com/{tenant}/v2.0。{tenant} はテナントの名前、テナント ID、または共通エンドポイントへの参照を表す *common* (マルチテナント アプリケーションで使用) です|
> |UseTokenLifetime |認証 Cookie が認証トークンのものと一致する必要があることを示します|
> |RequireHttpsMetadata     |メタデータ アドレスまたは登録機関の HTTPS を要求します。 この値は `True` に変更することをお勧めします|
> |TokenValidationParameters     | トークン検証のためのパラメーター リスト。 ここでは、`ValidateIssuer` は `false` に設定され、任意の個人、あるいは職場または学校のアカウント タイプからのサインインを受け付け可能であることを示しています|

### <a name="initiate-an-authentication-challenge"></a>認証チャレンジを開始する

**AccountController.cs** と同じように、コントローラーで認証チャレンジを要求することによって、ユーザーにサインインを求めることができます。

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> 上記のメソッドを使用した認証チャレンジの要求は省略可能であり、通常は、認証済みのユーザーと未認証のユーザーの両方がビューにアクセスできるようにするときに使用されます。 次のセクションで説明するメソッドを使用することで、コントローラーを保護できます。

### <a name="protect-a-controller-or-a-controllers-method"></a>コントローラーまたはコントローラーのメソッドを保護する

`[Authorize]` 属性を使用して、コントローラーまたはコントローラーのメソッドを保護できます。 この属性は、認証済みのユーザーのみを許可することによって、コントローラーまたはメソッドへのアクセスを制限します。つまり、ユーザーが認証されていない場合は、コントローラーにアクセスするための認証チャレンジを開始できます。

## <a name="next-steps"></a>次の手順

この ASP.NET Core クイック スタートの詳細を GitHub リポジトリで確認します。最新の ASP.NET Core Web アプリケーションに認証を追加する手順が含まれています。

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリのサンプル コード](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]