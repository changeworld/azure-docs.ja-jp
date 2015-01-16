<properties pageTitle="認証されたユーザーへのプッシュ通知の送信" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="特定の場所にプッシュ通知を送信する方法について説明します。 " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# 認証されたユーザーへのプッシュ通知の送信

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][プッシュ通知の使用]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを Notification Hubs に登録するにはユーザーの認証が必要になるように Mobile Services を変更します。登録も変更され、割り当てられたユーザー ID に基づいてタグが追加されます。最後に、サーバー スクリプトが更新されて、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されます。

このチュートリアルでは、次の手順について説明します。

+ [登録に認証が必要となるようにサービスを更新する]
+ [登録の前にログインするようにアプリケーションを更新する]
+ [アプリケーションをテストする]
 
このチュートリアルが対象とするのは、Android アプリケーションです。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>Notification Hubs を使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。 

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>insert 関数を次のコードに置き換え、<strong>[保存]</strong> をクリックします。</p>
<pre><code>function insert(item, user, request) {

    // Define a payload for the Google Cloud Messaging toast notification.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

<p>この insert スクリプトでは、ユーザー ID タグを使用することで、ログインしているユーザーによって作成されたすべての Google Cloud Messaging 登録に対してプッシュ通知が (挿入された項目のテキストと共に) 送信されます。</p></li></ol>

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##<a name="test"></a>アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 

<!---## <a name="next-steps"> </a>次のステップ

次の「[Service-side authorization of Mobile Services users (Mobile Services ユーザーのサービス側の認証)][Authorize users with scripts]」チュートリアルでは、認証されたユーザーに基づいて Mobile Services によって提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。.NET で Mobile Services を使用する方法の詳細については、[Mobile Services .NET の使用方法の概念リファレンス]を参照してください。

<!-- Anchors. -->
[登録に認証が必要となるようにサービスを更新する]: #register
[登録の前にログインするようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test
[次のステップ]:#next-steps


<!-- URLs. -->
[認証の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
