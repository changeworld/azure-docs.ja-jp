<properties
	pageTitle=""
	description="Azure Active Directory ウィザード実行後に Visual Studio プロジェクトの何が変更されるかについて説明します。"
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
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">プロジェクトの変更点</span>

リファレンスが追加されました。

#####NuGet パッケージのリファレンス

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET のリファレンス

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####コード ファイルがプロジェクトに追加された

認証スタートアップ クラス **App_Start/Startup.Auth.cs** が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。

#####スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、**Configuration** メソッドが更新されて `ConfigureAuth(app)` 呼び出しが含まれています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。


#####app.config ファイルまたは web.config ファイルに新しい構成値が含まれる

次の構成エントリが追加されました。<pre> `<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>` </pre>

###Azure AD アプリが作成された

ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

[Azure Active Directory の詳細を確認する](http://azure.microsoft.com/services/active-directory/)

###*[個々のユーザー アカウントの認証を無効にする]* がオンになっている場合、プロジェクトにはどのような変更が加えられますか。
NuGet パッケージのリファレンスが削除されるほか、ファイルが削除およびバックアップされます。プロジェクトの状態によっては、追加のリファレンスやファイルを手動で削除したり、必要に応じてコードを変更しなければならない場合があります。

#####削除される NuGet パッケージのリファレンス (存在する場合)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

#####バックアップおよび削除されるコード ファイル (存在する場合)

以下の各ファイルがバックアップされ、プロジェクトから削除されます。バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

#####バックアップされるコード ファイル (存在する場合)

以下の各ファイルがバックアップされてから置き換えられます。バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

###*[ディレクトリ データの読み取り]* がオンになっている場合、プロジェクトにはどのような変更が加えられますか。

#####app.config または web.config にさらに変更が加えられた

次の構成エントリがさらに追加されました。<pre>`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`</pre>

#####Azure Active Directory アプリが更新された
Azure Active Directory アプリが更新され、*[ディレクトリ データの読み取り]* アクセス許可が含まれるようになりました。また、`web.config` ファイルで *ida:Password* として使用される追加のキーが作成されました。

[Azure Active Directory の詳細を確認する](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=July15_HO4-->