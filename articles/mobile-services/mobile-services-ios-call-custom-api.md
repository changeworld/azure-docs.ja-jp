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
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# iOS クライアントからカスタム API を呼び出す方法 (JavaScript バックエンド)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、iOS アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用するとサーバー機能を持つカスタム エンドポイントを定義できますが、データベースの insert、update、delete、read 操作には対応しません。カスタム API を使用することにより、HTTP ヘッダーや本文の形式など、メッセージングをいっそう詳細に制御できます。

## <a name="define-custom-api"></a>カスタム API を定義する

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## 次のステップ

このトピックでは、**invokeApi** メソッドを使用して、iOS アプリからごく簡単なカスタム API を呼び出す方法を紹介しました。**invokeApi** メソッドの詳しい使用方法については、[Azure Mobile Services のカスタム API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) に関するページを参照してください。

また、次の Mobile Services のトピックの詳細を確認することをお勧めします。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存] <br/> ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[ソース管理へのサーバー スクリプトの保存]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->