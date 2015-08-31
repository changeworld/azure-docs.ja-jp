<properties
	pageTitle="Azure Mobile Apps を使用した iOS での認証の追加"
	description="Azure Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="krisragh"/>

# Azure Mobile Apps を使用した iOS の認証

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このチュートリアルでは、サポートされている ID プロバイダーを使用して、[iOS クイック スタート] プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [iOS のクイック スタート] チュートリアルを完了しておく必要があります。

##<a name="review"></a>サーバーのプロジェクト構成を確認する (省略可能)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="create-gateway"></a>App Service ゲートウェイを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>アプリケーションを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode で、**[実行]** をクリックして、アプリケーションを開始します。認証されないユーザーとしてアプリがバックエンドにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるため、例外が発生します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS のクイック スタート]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
[iOS クイック スタート]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=August15_HO8-->