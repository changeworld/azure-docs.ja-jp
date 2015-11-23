<properties 
	pageTitle="認証の検出時のエラー" 
	description="Active Directory の接続ウィザードで、サポートされていない認証の種類が検出された" 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="tarcher"/>

# 認証の検出時のエラー

ウィザードが以前の認証コードを検出するときに、互換性のない認証の種類が検出されました。

##確認内容

**注:** プロジェクトで以前の認証コードを正しく検出するには、プロジェクトを構築する必要があります。このエラーが発生したときに、プロジェクトに以前の認証コードがない場合は、再構築して、もう一度やり直してください。

###プロジェクトの種類

ウィザードは、プロジェクトに適切な認証ロジックを挿入できるように、開発しているプロジェクトの種類を確認します。プロジェクトに `ApiController` から派生したコントローラーが含まれている場合は、WebAPI プロジェクトと見なされます。プロジェクトに `MVC.Controller` から派生したコントローラーしか含まれていない場合は、MVC プロジェクトと見なされます。それ以外は、ウィザードでサポートされていないと見なされます。WebForms プロジェクトは、現時点でサポートされません。

###互換性のある認証コード

ウィザードは、以前にウィザードで構成されたか、ウィザードと互換性のある認証設定が存在するかどうかも確認します。すべての設定が存在する場合は、再入可能なケースと見なされ、ウィザードが開き、設定が表示されます。一部の設定のみが存在する場合は、エラー ケースと見なされます。

MVC プロジェクトの場合、ウィザードは、以前にウィザードを使用して構成された次の設定のいずれかが存在するかどうかを確認します。

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

さらに、以前にウィザードを使用して構成された、Web API プロジェクトの次の設定のいずれかが存在するかどうかを確認します。

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

###互換性のない認証コード

最後に、ウィザードは、Visual Studio の以前のバージョンで構成された認証コードのバージョンを検出しようと試みます。このエラーが発生した場合、プロジェクトで互換性のない認証の種類が検出されたことを表します。ウィザードは、Visual Studio の以前のバージョンに対して次の種類の認証を検出します。

* Windows 認証 
* 個々のユーザー アカウント 
* 組織アカウント 
 

MVC プロジェクトで Windows 認証を検出するために、ウィザードは **web.config** ファイルで `authentication` 要素を探します。

<pre>
	&lt;configuration>
	    &lt;system.web>
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /></span>
	    &lt;/system.web>
	&lt;/configuration>
</pre>

Web API プロジェクトで Windows 認証を検出するために、ウィザードはプロジェクトの **.csproj** ファイルで `IISExpressWindowsAuthentication` 要素を探します。

<pre>
	&lt;Project>
	    &lt;PropertyGroup>
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication>enabled&lt;/IISExpressWindowsAuthentication></span>
	    &lt;/PropertyGroup>
	&lt;/Project>
</pre>

個々のユーザー アカウント認証を検出するために、ウィザードは **Packages.config** ファイルで package 要素を探します。

<pre>
	&lt;packages>
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
	&lt;/packages>
</pre>

組織アカウント認証の古い形式を検出するために、ウィザードは **web.config** ファイルで次の要素を探します:

<pre>
	&lt;configuration>
	    &lt;appSettings>
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /></span>
	    &lt;/appSettings>
	&lt;/configuration>
</pre>

認証の種類を変更するには、互換性のない認証の種類を削除して、再度ウィザードを実行します。

詳細については、「[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。
 

<!---HONumber=Nov15_HO3-->