<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 処理内容

プロジェクトにリファレンスが追加されました

###### NuGet パッケージのリファレンス

Microsoft.IdentityModel.Protocol.Extensions、Microsoft.Owin、Microsoft.Owin.Host.SystemWeb、Microsoft.Owin.Security、Microsoft.Owin.Security.Cookies、Microsoft.Owin.Security.OpenIdConnect、Owin、System.IdentityModel.Tokens.Jwt

###### .NET のリファレンス

Microsoft.IdentityModel.Protocol.Extensions、Microsoft.Owin、Microsoft.Owin.Host.SystemWeb、Microsoft.Owin.Security、Microsoft.Owin.Security.Cookies、Microsoft.Owin.Security.OpenIdConnect、Owin、System、System.Data、System.Drawing、System.IdentityModel、System.IdentityModel.Tokens.Jwt、System.Runtime.Serialization

###### コード ファイルがプロジェクトに追加された

認証スタートアップ クラス App\_Start/Startup.Auth.cs が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。さらに、SignIn() メソッドおよび SignOut() メソッドを含むコントローラー クラス Controllers/AccountController.cs が追加されました。最後に、SignIn/SignOut のアクション リンクを含む部分ビュー Views/Shared/\_LoginPartial.cshtml が追加されました。

###### スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、Configuration() メソッドが更新されて ConfigureAuth(app) 呼び出しが追加されています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

###### app.config または web.config に新しい構成値が含まれる

次の構成エントリが追加されました。

    <pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

###### Azure Active Directory (AD) アプリが作成された

ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

## Azure Active Directory (AD) の使用

追加されたコードで実行できる操作を次に示します。

###### コントローラーへのアクセスに対して認証を要求する

プロジェクトに含まれるすべてのコントローラーには、[Authorize] 属性が設定されています。この属性により、ユーザーがこれらのコントローラーにアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。

###### SignIn/SignOut コントロールを追加する

ビューに SignIn/SignOut コントロールを追加するには、\_LoginPartial.cshtml 部分ビューを使用してこの機能をいずれかのビューに追加します。この機能を標準 \_Layout.cshtml ビューに追加した例を次に示します。
	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>
