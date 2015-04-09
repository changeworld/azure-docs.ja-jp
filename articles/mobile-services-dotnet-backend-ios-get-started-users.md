<properties
	pageTitle="既存の Azure Mobile Services アプリへの認証の追加 (iOS) | モバイル デベロッパー センター"
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# 既存の Azure Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このチュートリアルでは、サポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [Mobile Services のクイック スタート チュートリアル]を完了しておく必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Xcode でプロジェクトを開きます。**[Run]** を押してアプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) の例外が発生することを確認します。この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>アプリケーションに認証トークンを保存する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>次のステップ

次の [Mobile Services ユーザーのサービス側の承認]に関するチュートリアルではユーザー ID 値を使用して、返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、Mobile Services を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps
[アプリケーションに認証トークンを保存する]:#store-authentication

<!-- URLs. -->
[Mobile Services ユーザーのサービス側の承認]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services のクイック スタート チュートリアル]: mobile-services-dotnet-backend-ios-get-started.md
[データの使用]: mobile-services-dotnet-backend-ios-get-started-data.md
[認証の使用]: mobile-services-dotnet-backend-ios-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-ios-get-started-push.md
[スクリプトを使用したユーザーの承認]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure 管理ポータル]: https://manage.windowsazure.com/
[Mobile Services .NET の使用方法の概念リファレンス]: /develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->