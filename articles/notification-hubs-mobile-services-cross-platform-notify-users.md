<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="ユーザーへの通知 xplat モバイル サービス" pageTitle="通知ハブによるユーザーへのクロスプラットフォーム通知の送信 (モバイル サービス)" metaKeywords="" description="通知ハブ テンプレートを使用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブによるユーザーへのクロスプラットフォーム通知の送信" authors="glenga" solutions="" manager="" editor="" />

# 通知ハブによるユーザーへのクロスプラットフォーム通知の送信

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="モバイル サービス" class="current">モバイル サービス</a>
    <a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET">ASP.NET</a>
</div> 

前のチュートリアル「[通知ハブによるユーザーへの通知]」では、認証された特定のユーザーにより登録されたすべてのデバイスにプッシュ通知を行う方法について説明しました。そのチュートリアルでは、サポートされる各クライアント プラットフォームに通知を送信するため、複数の要求が必要でした。通知ハブでは、テンプレートがサポートされています。テンプレートを使用すると、特定のデバイスが通知を受信する方法を指定できます。これにより、クロスプラットフォーム通知の送信が簡単になります。このトピックでは、テンプレートを活用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を示します。テンプレートの詳細については、「[Azure 通知ハブの概要][Templates]」を参照してください。

<div class="dev-callout"><b>注</b>
	<p>通知ハブを使用すると、デバイスでは同じタグを持つ複数のテンプレートを登録できます。この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。これにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリケーションでバッジおよびトースト通知の両方として表示するなど)。</p>
</div>

次の手順を実行し、テンプレートを使用してクロスプラットフォーム通知を送信します。
	
1. [Azure 管理ポータル][Management Portal]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][0]

2. **[API]** タブをクリックし、API テーブルで **[register_notifications]** エントリをクリックします。

	![][1]

5. **[スクリプト]** タブをクリックし、`existingRegs` の値が **false** のときに新しい登録を作成する **else** ブロックを見つけ、次のコードで置き換えます。

		else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }
	
	このコードは、プラットフォーム固有のメソッドを呼び出して、ネイティブ登録の代わりにテンプレート登録を作成します。テンプレート登録がネイティブ登録から派生している場合、既存の登録を変更する必要はありません。

3. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][2]

2. **todoitem** で、**[スクリプト]** タブをクリックして **[挿入]** を選択し、既存の **insert** 関数を次のコードに置き換えます。
   
  	![][3]

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換えます。

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
					// Create a template-based payload.
		            var payload = '{ "message" : "New item added: ' + item.text + '" }';            
		            // Send a notification to the current user on all platforms. 
		            hub.send(user.userId, payload,  
		            function(error, outcome){
		                // Do something here with the outcome or error.
		            });     
		        }
		    });
		}

	このコードは、ネイティブ ペイロードを指定しなくても、すべてのプラットフォームに通知を同時に送信します。通知ハブは、登録されたテンプレートでの指定内容に従って、提供された_タグ_値を使用して適切なペイロードを作成して各デバイスに配信します。

4. スクリプトを更新して _`<NOTIFICATION_HUB_NAME>`_ と _`<FULL_SAS_CONNECTION_STRING>`_ を通知ハブ用の値に置き換え、**[保存]** をクリックします。

5. デバイス エミュレーターを停止するか、既存のクライアント アプリケーションをデバイスからアンインストールします。

	こうすることで、新しいインストール ID がモバイル サービス クライアントにより生成されます。これを行わない場合、サービスはテンプレート以外の既存の登録を使用しようとします。

5. もう一度クライアント アプリケーションを展開して実行し、登録に成功して新しい登録 ID が表示されることを確認します。

6. 前回と同様にテキストを入力し、新しい項目を追加します。	

	挿入が完了すると、アプリケーションは通知ハブからプッシュ通知を受け取ります。

7. (省略可能) クライアント アプリケーションを 2 つ目のデバイスに展開し、アプリケーションを実行してテキストを挿入します。

	各デバイスに通知が表示される点に注目してください。

## 次のステップ

このチュートリアルを完了したら、以下のトピックで通知ハブとテンプレートの詳細を参照してください。

+ **通知ハブを使用したニュース速報の送信 ([Windows ストア C#][Breaking news .NET]/[iOS][Breaking news iOS])**<br/>テンプレートの使用に関する別のシナリオを示します。

+  **[Azure 通知ハブの概要][Templates]**<br/>この概要トピックでは、テンプレートについて詳細に説明されています。

+  **[方法: Azure 通知ハブ (Windows ストア アプリ)]** <br/> テンプレート式言語のリファレンスが記載されています。



<!-- Anchors. -->
[ASP.NET バックエンド]: #aspnet
[モバイル サービス バックエンド]: #mobileservices

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
<!-- URLs. -->
[ASP.NET ユーザーへのプッシュ通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet/
[モバイル サービス ユーザーへのプッシュ通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[管理ポータル]: https://manage.windowsazure.com/
[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[ニュース速報 .NET]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[ニュース速報 iOS]: /ja-jp/manage/services/notification-hubs/breaking-news-ios
[Azure 通知ハブ]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users 
[テンプレート]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[方法: Azure 通知ハブ (Windows ストア アプリ)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927172.aspx

