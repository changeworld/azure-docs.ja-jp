<properties
	pageTitle="MVC プロジェクト (Visual Studio Azure Active Directory 接続済みサービス) の変更点 | Microsoft Azure"
	description="Visual Studio 接続済みサービスを使用して Azure AD に接続するときに MVC プロジェクトで何が起こるかについて説明します。"
	services="active-directory"
	documentationCenter="na"
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# MVC プロジェクト (Visual Studio Azure Active Directory 接続済みサービス) の変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)



## リファレンスが追加されました

### NuGet パッケージのリファレンス

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### .NET のリファレンス

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## コードが追加されました

### コード ファイルがプロジェクトに追加された

認証スタートアップ クラス **App\_Start/Startup.Auth.cs** が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。さらに、**SignIn(**) メソッドと **SignOut()** メソッドを含むコントローラー クラス Controllers/AccountController.cs が追加されました。最後に、SignIn/SignOut のアクション リンクを含む部分ビュー **Views/Shared/\_LoginPartial.cshtml** が追加されました。

### スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、**Configuration** メソッドが **ConfigureAuth(app)** 呼び出しを含むように更新されました。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

### app.config または web.config に新しい構成値が含まれる

次の構成エントリが追加されました。


	<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
	    <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
	</appSettings>

### Azure Active Directory (AD) アプリが作成された
ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

##*[個々のユーザー アカウントの認証を無効にする]* がオンになっている場合にプロジェクトに対して行われた追加の変更
NuGet パッケージのリファレンスが削除されるほか、ファイルが削除およびバックアップされます。プロジェクトの状態によっては、追加のリファレンスやファイルを手動で削除したり、必要に応じてコードを変更しなければならない場合があります。

### 削除される NuGet パッケージのリファレンス (存在する場合)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### バックアップおよび削除されるコード ファイル (存在する場合)

以下の各ファイルがバックアップされ、プロジェクトから削除されます。バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

- **App\_Start\\IdentityConfig.cs**
- **Controllers\\ManageController.cs**
- **Models\\IdentityModels.cs**
- **Models\\ManageViewModels.cs**

### バックアップされるコード ファイル (存在する場合)

以下の各ファイルがバックアップされてから置き換えられます。バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

- **Startup.cs**
- **App\_Start\\Startup.Auth.cs**
- **Controllers\\AccountController.cs**
- **Views\\Shared\_LoginPartial.cshtml**

## *[ディレクトリ データの読み取り]* がオンになっている場合にプロジェクトに対して行われた追加の変更

リファレンスがさらに追加されました。

###NuGet パッケージの追加リファレンス

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###追加の .NET リファレンス

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###コード ファイルがプロジェクトにさらに追加された

トークンのキャッシュをサポートするために次の 2 つのファイルが追加されました。**Models\\ADALTokenCache.cs** と **Models\\ApplicationDbContext.cs**。Azure Graph API を使用するユーザー プロファイル情報へのアクセスについて説明するために、コント ローラーとビューがさらに追加されました。これらのファイルは **Controllers\\UserProfileController.cs** と **Views\\UserProfile\\Index.cshtml** です。

###スタートアップ コードがプロジェクトにさらに追加された

**startup.auth.cs** で、新しい **OpenIdConnectAuthenticationNotifications** オブジェクトが **OpenIdConnectAuthenticationOptions** の **Notifications** メンバーに追加されました。これは、OAuth コードを受信できるようにして、アクセス トークンと交換するためのものです。

###app.config または web.config にさらに変更が加えられた

次の構成エントリがさらに追加されました。

	<appSettings>
	    <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
	</appSettings>

次の構成セクションと接続文字列が追加されました。

	<configSections>
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
	</entityFramework>


###Azure Active Directory アプリが更新された
Azure Active Directory アプリが更新され、*ディレクトリ データの読み取り*アクセス許可が含まれるようになりました。また、**web.config** ファイルで *ida:ClientSecret* として使用される追加のキーが作成されました。

[Azure Active Directory の詳細を確認する](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=Oct15_HO3-->