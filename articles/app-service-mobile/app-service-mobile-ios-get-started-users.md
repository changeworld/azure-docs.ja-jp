<properties
	pageTitle="Azure Mobile Apps を使用した iOS での認証の追加"
	description="Azure Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="krisragh"/>

# iOS アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このチュートリアルでは、サポートされている ID プロバイダーを使用して、[iOS クイック スタート] プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [iOS のクイック スタート] チュートリアルを完了しておく必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode で、**[Run]** をクリックしてアプリケーションを開始します。認証されないユーザーとしてアプリがバックエンドにアクセスしようとしても、_TodoItem_ テーブルで認証が要求されるために例外が発生します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS のクイック スタート]: app-service-mobile-ios-get-started.md
[iOS クイック スタート]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->