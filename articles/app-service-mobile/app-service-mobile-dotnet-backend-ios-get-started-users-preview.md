<properties 
	pageTitle="iOS でのモバイル アプリの認証の使用" 
	description="モバイル アプリを使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="mattchenderson,krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# iOS アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。また、先に [iOS アプリの作成]に関するチュートリアルを完了している必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>Xcode で、プロジェクトを開きます。<b>[実行]</b> をクリックし、アプリケーションを起動します。アプリケーションの起動後に、状態コード 401 (許可されていません) の例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されていないユーザーとしてアプリケーションがモバイル アプリ コードにアクセスしようとしても、現在の <em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>アプリケーションに認証トークンを保存する

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[iOS アプリの作成]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
<!--HONumber=54--> 