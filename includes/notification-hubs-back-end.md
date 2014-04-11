
このセクションでは、通知を送信するための 2 つの方法について説明します。

- [コンソール アプリケーションの使用]
- [モバイル サービスの使用]

どちらのバックエンドも、Windows ストア デバイスと iOS デバイスの両方に通知を送信します。[通知ハブ REST インターフェイス]を使用することで、バックエンドから通知を送信できます。

<h3><a name="console"></a>C# のコンソール アプリケーションを使用して通知を送信するには</h3>

「[Get started with Notification Hubs (通知ハブの使用)][get-started]」を実行したときにコンソール アプリケーションを作成した場合は、ステップ 1. ～ 3. はスキップします。

1. Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。

   	![][13]

2. Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックし、コンソール ウィンドウで次のコマンドを入力して、**Enter** キーを押します。

        Install-Package WindowsAzure.ServiceBus
 	
	これにより、<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Windows Azure のサービス バス SDK へ参照が追加されます。

3. Program.cs ファイルを開き、次の "using" ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4. "Program" クラス内で、次のメソッドを追加するか、既にメソッドが指定されている場合は置き換えます。

        private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";
                    // Send a WNS notification using the template.            
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";
                    // Send an MPNS notification using the template.            
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    // Send an APNS notification using the template.
                    await hub.SendAppleNativeNotificationAsync(alert, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	このコードにより、文字列配列内の 6 つのタグそれぞれに対応した通知が、Windows ストア デバイス、Windows Phone デバイス、iOS デバイスに送信されます。タグを使用することで、デバイスは登録されているカテゴリに関する通知のみを確実に受信できます。
	
	<div class="dev-callout"><strong>注</strong>
		<p>このバックエンド コードは、Windows ストア クライアント、Windows Phone クライアント、iOS クライアントをサポートします。send メソッドは、通知ハブが特定のクライアント プラットフォームに対してまだ構成されていない場合、エラー応答を返します。</p>
	</div>

6. 上のコードで、"<hub name>" と "<connection string with full access>" のプレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えます。

7. **Main** メソッド内に、次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

これで、「[アプリケーションを実行して通知を生成する]」の手順を実行できます。

###<a name="mobile-services"></a>モバイル サービスを使用して通知を送信するには

モバイル サービスを使用して通知を送信するには、次のステップを実行します。

0.チュートリアル「[Get started with Mobile Services (モバイル サービスの使用)]」を実行して、モバイル サービスを作成します。

1. [Windows Azure の管理ポータル]にログオンし、[モバイル サービス] をクリックして、目的のモバイル サービスをクリックします。

2. **[スケジューラ]** タブをクリックし、**[作成]** をクリックします。

   	![][15]

3. **[新しいジョブの作成]** で、ジョブの名前を入力し、**[要求時]** をクリックします。次にチェック マークをクリックして、これらの設定を受け入れます。

   	![][16]

4. ジョブが作成されたら、ジョブ名をクリックし、**[スクリプト]** タブで、スケジュールされたジョブの関数内に次のスクリプトを挿入します。

	    var azure = require('azure');
	    var notificationHubService = azure.createNotificationHubService(
				'<hub name>', 
				'<connection string with full access>');

   		 // Create an array of breaking news categories.
		    var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
		    for (var i = 0; i < categories.length; i++) {
		        // Send a WNS notification.
		        notificationHubService.wns.sendToastText01(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send a MPNS notification.
		        notificationHubService.mpns.sendToast(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send an APNS notification.
		        notificationHubService.apns.send(categories[i], {
		            alert: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		    }

	このコードにより、文字列配列内の 6 つのタグそれぞれに対応した通知が、Windows ストア デバイス、Windows Phone デバイス、iOS デバイスに送信されます。タグを使用することで、デバイスは登録されているカテゴリに関する通知のみを確実に受信できます。

6. 上のコードで、"<hub name>" と "<connection string with full access>" のプレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えます。

7. スケジュールされたジョブの関数の後に次のヘルパー関数を追加し、**[保存]** をクリックします。
	
        function sendComplete(error) {
 		   if (error) {
	            // An exception is raised when there are no devices registered for 
	            // the iOS, Windows Store, or Windows Phone platforms. Consider using template 
	            // notifications instead.
	            //console.warn("Notification failed:" + error);
	        }
	    }
	
	<div class="dev-callout"><strong>注</strong>
		<p>このコードは、Windows ストア クライアント、Windows Phone クライアント、iOS クライアントをサポートします。send メソッドは、特定のプラットフォームに対して登録が存在しない場合、エラー応答を返します。これを回避するには、テンプレート登録を使用して、1 つの通知を複数のプラットフォームに送信することを考慮してください。例については、「<a href="/ja-jp/manage/services/notification-hubs/breaking-news-localized-dotnet/">Use Notification Hubs to broadcast localized breaking news (通知ハブを使用してローカライズされたニュース速報をブロードキャストする)</a>」を参照してください。</p>
	</div>

これで、「[アプリケーションを実行して通知を生成する]」の手順を実行できます。

<!-- Anchors -->
[コンソール アプリケーションの使用]: #console
[モバイル サービスの使用]: #mobile-services
[アプリケーションを実行して通知を生成する]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started (作業を開始する)]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Use Notification Hubs to send notifications to users (通知ハブを使用したユーザーへの通知の送信)]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[Windows Azure の管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[Notification Hubs How-To for Windows Store (方法: Windows Azure 通知ハブ (Windows ストア アプリ))]: http://msdn.microsoft.com/ja-jp/library/jj927172.aspx
[通知ハブの REST API]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx

