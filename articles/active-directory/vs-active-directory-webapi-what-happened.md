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
 

<!---HONumber=62-->