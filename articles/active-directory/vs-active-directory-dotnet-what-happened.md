<properties 
	pageTitle="Active Directory 認証の使用 - 変更内容" 
	description="Visual Studio の Azure Active Directory プロジェクトでの変更内容について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)

###<span id="whathappened">プロジェクトの変更点</span>
 
リファレンスが追加されました。

#####NuGet パッケージのリファレンス

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET のリファレンス

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####コード ファイルがプロジェクトに追加された 

認証スタートアップ クラス `App_Start/Startup.Auth.cs` が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。さらに、`SignIn()` メソッドおよび `SignOut()` メソッドを含むコントローラー クラス Controllers/AccountController.cs が追加されました。最後に、SignIn/SignOut のアクション リンクを含む部分ビュー `Views/Shared/_LoginPartial.cshtml` が追加されました。

#####スタートアップ コードがプロジェクトに追加された
 
既にプロジェクトに Startup クラスがある場合、**Configuration** メソッドが更新されて `ConfigureAuth(app)` 呼び出しが含まれています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

#####app.config または web.config に新しい構成値が含まれる 

次の構成エントリが追加されました。<pre> `<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/" /> 
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Azure Active Directory (AD) アプリが作成された 
ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

###*[ディレクトリ データの読み取り]* チェック ボックスをオンにした場合は、プロジェクトがさらに変更されています。
リファレンスがさらに追加されました。

#####NuGet パッケージの追加リファレンス

- `EntityFramework`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `System.Spatial`

#####追加の .NET リファレンス

- `EntityFramework`
- `EntityFramework.SqlServer`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms`
- `System.Spatial`

#####コード ファイルがプロジェクトにさらに追加された 

トークン キャッシュをサポートするために 2 つのファイル (`Models\ADALTokenCache.cs` と `Models\ApplicationDbContext.cs`) が追加されました。Azure Graph API を使用するユーザー プロファイル情報へのアクセスについて説明するために、コント ローラーとビューがさらに追加されました。これらのファイルは `Controllers\UserProfileController.cs` と `Views\UserProfile\Index.cshtml` です。

#####スタートアップ コードがプロジェクトにさらに追加された
 
`startup.auth.cs` ファイルでは、新しい `OpenIdConnectAuthenticationNotifications` オブジェクトが `OpenIdConnectAuthenticationOptions` の `Notifications` メンバーに追加されました。これは、OAuth コードを受信できるようにして、アクセス トークンと交換するためのものです。

#####app.config または web.config にさらに変更が加えられた

次の構成エントリがさらに追加されました。<pre>`<appSettings>
	    <add key="Ida:ClientSecret" value="Your Azure AD App's new client secret" /> 
	</appSettings>`</pre>

次の構成セクションと接続文字列が追加されました。<pre> `<configSections>
	    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
	    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
	</configSections>
	<connectionStrings>
	    <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
	</connectionStrings>
	<entityFramework>
	    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
	      <parameters>
	        <parameter value="mssqllocaldb" />
	      </parameters>
	    </defaultConnectionFactory>
	    <providers>
	      <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
	    </providers>
	</entityFramework>`</pre>


#####Azure Active Directory アプリが更新された
Azure Active Directory アプリが更新され、*[ディレクトリ データの読み取り]* アクセス許可が含まれるようになりました。また、`web.config` ファイルで *ClientSecret* として使用される追加のキーが作成されました。

[Azure Active Directory の詳細を確認する](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO3-->