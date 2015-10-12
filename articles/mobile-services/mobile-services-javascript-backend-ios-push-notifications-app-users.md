<properties
	pageTitle="iOS の認証されたユーザーへのプッシュ通知の送信 (JavaScript バックエンド)"
	description="特定のユーザーにプッシュ通知を送信する方法について説明します。"
	services="mobile-services,notification-hubs"
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
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

このトピックでは、iOS 上の認証されたユーザーにプッシュ通知を送信する方法を説明します。このチュートリアルを開始する前に、まず、「[認証の使用]」 と 「[プッシュ通知の使用]」を実行してください。

このチュートリアルでは、まずユーザーに認証を受けるように求める必要があります。次にプッシュ通知用の通知ハブに登録します。さらに、認証されたユーザーのみに通知が送信されるようにサーバー スクリプトを更新します。


##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

`insert` 関数を次のコードに置き換え、[**保存**] をクリックします。次の insert スクリプトでは、ユーザー ID タグを使用して、ログインしたユーザーからのすべての iOS アプリケーション登録にプッシュ通知を送信します。

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[認証の使用]: mobile-services-ios-get-started-users.md
[プッシュ通知の使用]: mobile-services-javascript-backend-ios-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=Oct15_HO1-->