<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 処理内容

プロジェクトにリファレンスが追加されました

###### NuGet パッケージのリファレンス

Microsoft.Owin、Microsoft.Owin.Host.SystemWeb、Microsoft.Owin.Security、Microsoft.Owin.Security.ActiveDirectory、Microsoft.Owin.Security.Jwt、Microsoft.Owin.Security.OAuth、Newtonsoft.Json、Owin、System.IdentityModel.Tokens.Jwt

###### .NET のリファレンス

Microsoft.Owin、Microsoft.Owin.Host.SystemWeb、Microsoft.Owin.Security、Microsoft.Owin.Security.ActiveDirectory、Microsoft.Owin.Security.Jwt、Microsoft.Owin.Security.OAuth、Newtonsoft.Json、Owin、System.IdentityModel.Tokens.Jwt

###### コード ファイルがプロジェクトに追加された

認証スタートアップ クラス App\_Start/Startup.Auth.cs が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。

###### スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、Configuration() メソッドが更新されて ConfigureAuth(app) 呼び出しが追加されています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

###### app.config ファイルまたは web.config ファイルに新しい構成値が含まれる

次の構成エントリが追加されました。
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

### Azure Active Directory (AD) アプリが作成された

ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

### Azure Active Directory (AD) の使用

追加されたコードで実行できる操作を次に示します。

###### コントローラーへのアクセスに対して認証を要求する

プロジェクトに含まれるすべてのコントローラーには、Authorize 属性が設定されています。この属性により、ユーザーがこれらのコントローラーによって定義された API にアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

