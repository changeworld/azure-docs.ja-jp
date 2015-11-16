<properties 
	pageTitle="認証されたユニバーサル Windows アプリ ユーザーにプッシュ通知を送信します。" 
	description="Azure Mobile Services から、ユニバーサル Windows C# アプリの特定のユーザーにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="glenga"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##概要
このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の「[アプリケーションにプッシュ通知を追加する]」チュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー スクリプトが更新されて、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されます。

このチュートリアルでは、次の手順について説明します。

1. [登録に認証が必要となるようにサービスを更新する]
2. [登録の前にログインするようにアプリケーションを更新する]
3. [アプリケーションをテストする]
 
このチュートリアルが対象とするのは、Windows ストアと Windows Phone ストアの両方のアプリケーションです。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [アプリに認証を追加する] <br/>TodoList サンプル アプリにログイン要件を追加します。

+ [アプリケーションにプッシュ通知を追加する]<br/>Notification Hubs を使用してプッシュ通知を送信するように TodoList サンプル アプリを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5.insert 関数を次のコードに置き換え、**[保存]** をクリックします。

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;この insert スクリプトでは、ユーザー ID タグを使用することで、ログインしているユーザーによって作成されたすべての Windows ストア アプリケーション登録に対してプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[登録に認証が必要となるようにサービスを更新する]: #register
[登録の前にログインするようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[アプリに認証を追加する]: ../mobile-services-windows-store-dotnet-get-started-users.md
[アプリケーションにプッシュ通知を追加する]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Nov15_HO2-->