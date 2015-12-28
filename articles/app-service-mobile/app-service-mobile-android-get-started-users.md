<properties
	pageTitle="Mobile Apps を使用した Android での認証の追加 | Azure App Service"
	description="Azure App Service で Mobile Apps を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Android アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/06/2015"
	ms.author="glenga"/>

# Android アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## まとめ

このチュートリアルでは、サポートされている ID プロバイダーを使用して、Android で todolist クイック スタート プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [Mobile Apps の使用]チュートリアルを完了しておく必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Android Studio で、[Mobile Apps の使用]チュートリアルの完了時に作成されたプロジェクトを開き、**[Run]** メニューから **[Run app]** をクリックし、アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

	 この問題は、認証されないユーザーとしてアプリケーションがバックエンドにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、Mobile App バックエンドのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>クライアントに認証トークンをキャッシュする

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##次のステップ

これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

+ [Android アプリにプッシュ通知を追加する](app-service-mobile-android-get-started-push.md) アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使用してプッシュ通知を送信するように Mobile App バックエンドを構成する方法について説明します。

+ [Android アプリのオフライン同期を有効にする](app-service-mobile-android-get-started-offline-data.md) Mobile App バックエンドを使用して、オフライン サポートをアプリに追加する方法について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Mobile Apps の使用]: app-service-mobile-android-get-started.md

<!----HONumber=AcomDC_1217_2015-->