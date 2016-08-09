<properties
	pageTitle="既存の Azure Mobile Services アプリへの認証の追加 (iOS) | JavaScript バックエンド | Microsoft Azure"
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="krisragh"/>

# 既存の Azure Mobile Services アプリに認証を追加する

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> このトピックの Mobile Apps バージョンについては、「[Add authentication to your iOS App (iOS アプリに認証を追加する)](../app-service-mobile/app-service-mobile-ios-get-started-users.md)」を参照してください。

このチュートリアルでは、サポートされている ID プロバイダーを使用して、[Mobile Services クイックスタート チュートリアル]に認証を追加します。

[Mobile Services クイック スタート チュートリアル]を完了しておくことをお勧めします。代わりに、Quick Start iOS プロジェクトをダウンロードすることもできます。[Azure クラシック ポータル]で、**[Mobile Services]**、目的のモバイル サービスの順にクリックし、左上のクラウド マークをクリックします。次に、**[iOS]**、**[新しい iOS アプリを作成する]**、**[アプリケーションをダウンロードして実行する]**、**[Objective-C]**、**[ダウンロード]** の順にクリックします。テーブルをまだ作成していない場合は、**[TodoItem テーブルを作成する]** をクリックしてから、**[ダウンロード]** をクリックしてください。

##<a name="register"></a>認証用のアプリを登録する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>データへのアクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

##<a name="add-authentication"></a>アプリに認証を追加する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>アプリに認証トークンを保存する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>次のステップ

次に、[ユーザー ID 値を使用して、返されたデータをフィルター処理する方法](mobile-services-javascript-backend-service-side-authorization.md)について説明します。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Service-side authorization of Mobile Services users]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Mobile Services クイック スタート チュートリアル]: /develop/mobile/tutorials/get-started-ios
[Mobile Services クイックスタート チュートリアル]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure クラシック ポータル]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->