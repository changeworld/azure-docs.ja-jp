<properties
	pageTitle="iOS クライアントからカスタム API を呼び出す方法"
	description="カスタム API を定義し、Azure Mobile Services を使用する iOS アプリからそれを呼び出す方法について説明します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/03/2015"
	ms.author="krisragh"/>


# iOS クライアントからカスタム API を呼び出す方法 (.NET バックエンド)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、iOS アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用するとサーバー機能を持つカスタム エンドポイントを定義できますが、データベースの insert、update、delete、read 操作には対応しません。カスタム API を使用することにより、HTTP ヘッダーや本文の形式など、メッセージングをいっそう詳細に制御できます。

## <a name="define-custom-api"></a>カスタム API を定義する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

このトピックでは、**invokeApi** メソッドを使用して、iOS アプリからごく簡単なカスタム API を呼び出す方法を紹介しました。**invokeApi** メソッドの詳しい使用方法については、[Azure Mobile Services のカスタム API に関する投稿](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)を参照してください。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Mobile Services Quick Start]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Store server scripts in source control]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->