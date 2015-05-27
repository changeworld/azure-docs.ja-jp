<properties 
	pageTitle="認証されたユーザーへのプッシュ通知の送信" 
	description="特定の場所にプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="glenga"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][Get started with push notifications]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー スクリプトが更新されて、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されます。

このチュートリアルでは、次の手順について説明します。

1. [登録に認証が必要となるようにサービスを更新する]
2. [登録の前にログインするようにアプリケーションを更新する]
3. [アプリケーションをテストする]
 
このチュートリアルが対象とするのは、Windows Phone 8.0 および Windows Phone 8.1 Silverlight のアプリケーションです。Windows Phone 8.1 ストアのアプリケーションについては、[このトピックの Windows ストア版](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md)を参照してください。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>通知ハブを使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>insert 関数を次のコードに置き換え、<strong>[保存]</strong> をクリックします。</p>
<pre><code>function insert(item, user, request) {
	// Define a payload for the Windows Phone toast notification.
	var payload = '&lt;?xml version="1.0" encoding="utf-8"?>' +
		'&lt;wp:Notification xmlns:wp="WPNotification">&lt;wp:Toast>' +
		'&lt;wp:Text1>New Item&lt;/wp:Text1>&lt;wp:Text2>' + item.text + 
		'&lt;/wp:Text2>&lt;/wp:Toast>&lt;/wp:Notification>';

	// Get the ID of the logged-in user.
	var userId = user.userId;		

	request.execute({
		success: function() {
			// If the insert succeeds, send a notification.
			push.mpns.send(userId, payload, 'toast', 22, {
				success: function(pushResponse) {
					console.log("Sent push:", pushResponse);
					request.respond();
					},             
					error: function (pushResponse) {
						console.log("Error Sending push:", pushResponse);
						request.respond(500, { error: pushResponse });
						{
					});
				{
			});      
}</code></pre>

<p>この insert スクリプトでは、ユーザー ID タグを使用することで、ログインしているユーザーによって作成されたすべての Windows Phone (MPNS) アプリケーション登録に対してプッシュ通知が (挿入された項目のテキストと共に) 送信されます。</p></li></ol>

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

<!-- Anchors. -->
[登録に認証が必要となるようにサービスを更新する]: #register
[登録の前にログインするようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[認証の使用]: mobile-services-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-phone-get-started-push.md
[プッシュ通知の使用]: mobile-services-javascript-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=54-->