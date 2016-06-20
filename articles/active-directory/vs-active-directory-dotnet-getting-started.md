<properties 
	pageTitle="Azure Active Directory と Visual Studio 接続済みサービスの概要 (MVC プロジェクト) | Microsoft Azure" 
	description="Visual Studio 接続済みサービスを使用して Azure Active Directory を接続または作成した後に、MVC プロジェクトで Azure AD の使用を開始する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="tarcher"/>

# Azure Active Directory と Visual Studio 接続済みサービスの概要 (MVC プロジェクト)

> [AZURE.SELECTOR]
> - [作業の開始](vs-active-directory-dotnet-getting-started.md)
> - [変更内容](vs-active-directory-dotnet-what-happened.md)
 
##コントローラーへのアクセスに対して認証を要求する 

プロジェクトに含まれるすべてのコントローラーには、**Authorize** 属性が設定されています。この属性により、ユーザーがこれらのコントローラーにアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。
 
##SignIn/SignOut コントロールを追加する 

ビューに SignIn/SignOut コントロールを追加するには、**\_LoginPartial.cshtml** 部分ビューを使用してこの機能をいずれかのビューに追加します。この機能を標準 **\_Layout.cshtml** ビューに追加した例を次に示します。(div class="navbar-collapse collapse" の最後の要素にご注目ください):

<pre>
    &lt;!DOCTYPE html> 
     &lt;html> 
     &lt;head> 
         &lt;meta charset="utf-8" /> 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        &lt;title>@ViewBag.Title - My ASP.NET Application&lt;/title> 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head> 
    &lt;body> 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"> 
            &lt;div class="container"> 
                &lt;div class="navbar-header"> 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                    &lt;/button> 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div> 
                &lt;div class="navbar-collapse collapse"> 
                    &lt;ul class="nav navbar-nav"> 
                        &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li> 
                    &lt;/ul> 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div> 
            &lt;/div> 
        &lt;/div> 
        &lt;div class="container body-content"> 
            @RenderBody() 
            &lt;hr /> 
            &lt;footer> 
                &lt;p>&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p> 
            &lt;/footer> 
        &lt;/div> 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body> 
    &lt;/html>
</pre>

[Azure Active Directory の詳細を確認する](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0608_2016-->