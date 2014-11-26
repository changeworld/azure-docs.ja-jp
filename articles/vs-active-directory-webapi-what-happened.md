<properties title="Active Directory 認証の使用" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

### <span id="whathappened">プロジェクトの変更点</span>

リファレンスが追加されました。

##### NuGet パッケージのリファレンス

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### .NET のリファレンス

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### コード ファイルがプロジェクトに追加された

認証スタートアップ クラス **App\_Start/Startup.Auth.cs** が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。

##### スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、**Configuration** メソッドが更新されて `ConfigureAuth(app)` 呼び出しが追加されています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

##### app.config ファイルまたは web.config ファイルに新しい構成値が含まれる

次の構成エントリが追加されました。

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Azure Active Directory (AD) アプリが作成された

ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

[Azure Active Directory の詳細を確認する][Azure Active Directory の詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [変更内容]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Azure Active Directory の詳細を確認する]: http://azure.microsoft.com/services/active-directory/
