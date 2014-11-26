<properties title="Active Directory 認証の使用" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Azure Active Directory の使用 (.NET プロジェクト)

##### コントローラーへのアクセスに対して認証を要求する

プロジェクトに含まれるすべてのコントローラーには、**Authorize** 属性が設定されています。この属性により、ユーザーがこれらのコントローラーにアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

##### SignIn/SignOut コントロールを追加する

ビューに SignIn/SignOut コントロールを追加するには、\*\*\_LoginPartial.cshtml\*\* 部分ビューを使用してこの機能をいずれかのビューに追加します。この機能を標準 \*\*\_Layout.cshtml\*\* ビューに追加した例を次に示します。(div class="navbar-collapse collapse" の最後の要素にご注目ください):

<pre class="prettyprint">
&lt;!DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset=&quot;utf-8&quot; /&gt; 
&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render(&quot;~/Content/css&quot;) 
@Scripts.Render(&quot;~/bundles/modernizr&quot;) 
&lt;/head&gt; 
&lt;body&gt; 
&lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
&lt;div class=&quot;container&quot;&gt; 
&lt;div class=&quot;navbar-header&quot;&gt; 
&lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
&lt;/div&gt; 
&lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
&lt;ul class=&quot;nav navbar-nav&quot;&gt; 
&lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
&lt;/ul&gt; 
@Html.Partial(&quot;_LoginPartial&quot;) 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class=&quot;container body-content&quot;&gt; 
@RenderBody() 
&lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render(&quot;~/bundles/jquery&quot;) 
@Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
@RenderSection(&quot;scripts&quot;, required:false) 
&lt;/body&gt; 
&lt;/html&gt;
</pre>

[Azure Active Directory の詳細を確認する][Azure Active Directory の詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [変更内容]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Azure Active Directory の詳細を確認する]: http://azure.microsoft.com/services/active-directory/
