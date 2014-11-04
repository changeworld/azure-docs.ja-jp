<properties title="認証の検出時のエラー" pageTitle="認証の検出時のエラー" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 認証の検出時のエラー

ウィザードが以前の認証コードを検出するときに、互換性のない認証の種類が検出されました。

##### 確認内容

ウィザードは、Visual Studio の以前のバージョンで構成された認証コードのバージョンを検出しようと試みます。このエラーが発生した場合、プロジェクトで互換性のない認証の種類が検出されたことを表します。ウィザードは、Visual Studio の以前のバージョンに対して次の種類の認証を検出します。

-   Windows 認証
-   個々のユーザー アカウント
-   組織アカウント

MVC プロジェクトで Windows 認証を検出するために、ウィザードは **web.config** ファイルで `authentication` 要素を探します。

<pre class="prettyprint">
&lt;configuration&gt;
&lt;system.web&gt;
&lt;authentication mode=&quot;Windows&quot; /&gt;
&lt;/system.web&gt;
&lt;/configuration&gt;
</pre>

Web API プロジェクトで Windows 認証を検出するために、ウィザードは **.csproj** ファイルで `IISExpressWindowsAuthentication` 要素を探します。

<pre class="prettyprint">
&lt;Project&gt;
&lt;PropertyGroup&gt;
&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
&lt;/PropertyGroup&gt;
&lt;/Project&gt;
</pre>

個々のユーザー アカウント認証を検出するために、ウィザードは **Packages.config** ファイルで package 要素を探します。

<pre class="prettyprint">
&lt;packages&gt;
&lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
&lt;/packages&gt;
</pre>

組織アカウント認証の古い形式を検出するために、ウィザードは **web.config** ファイルで次の要素を探します:

<pre class="prettyprint">
&lt;configuration*gt;
&lt;appSettings&gt;
&lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
&lt;/appSettings&gt;
&lt;/configuration&gt;
</pre>

認証の種類を変更するには、互換性のない認証の種類を削除して、再度ウィザードを実行します。

詳細については、「[Azure AD の認証シナリオ][Azure AD の認証シナリオ]」を参照してください。

  [Azure AD の認証シナリオ]: http://msdn.microsoft.com/library/azure/dn499820.aspx
