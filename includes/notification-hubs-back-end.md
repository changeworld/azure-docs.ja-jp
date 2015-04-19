
このセクションでは、.NET コンソール アプリケーションおよびその他のアプリケーションから通知を送信する方法を示します。
Mobile Services を使用している場合は、「[Mobile Services アプリへのプッシュ通知の追加](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md)」のチュートリアルを参照してください。Java または PHP を使用する場合は、「[Java/PHP から Notification Hubs を使用する方法](notification-hubs-java-backend-how-to.md)」を参照してください。[通知ハブ REST インターフェイス]を使用することで、バックエンドから通知を送信できます。

次のコードでは、Windows ストア デバイス、Windows Phone デバイス、iOS デバイス、Android デバイスに通知を送信します。 

[[通知ハブの使用]][get-started] を実行したときにコンソール アプリケーションを作成した場合は、ステップ 1 ～ 3 はスキップします。

1. Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。 

   	![][13]

2. Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックし、コンソール ウィンドウで次のコマンドを入力して、**Enter** キーを押します。

        Install-Package WindowsAzure.ServiceBus
 	
	これにより、<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure Service Bus SDK への参照が追加されます。 

3. Program.cs ファイルを開き、次の  `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4.  `Program` クラス内で、次のメソッドを追加するか、既にメソッドが指定されている場合は置き換えます。

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
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	このコードにより、文字列配列内の 6 つのタグそれぞれに対応した通知が、Windows ストア デバイス、Windows Phone デバイス、iOS デバイスに送信されます。タグを使用することで、デバイスは登録されているカテゴリに関する通知のみを確実に受信できます。
	
	> [AZURE.NOTE] このバックエンド コードは、Windows ストア クライアント、Windows Phone クライアント、iOS クライアント、Android クライアントをサポートします。send メソッドは、通知ハブが特定のクライアント プラットフォームに対してまだ構成されていない場合、エラー応答を返します。 

6. 上記のコードで `<hub name>` と `<connection string with full access>` のプレースホルダーを通知ハブ名に、接続文字列を既に取得済みの  *DefaultFullSharedAccessSignature* に置き換えます。

7. **Main** メソッド内に、次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[コンソール アプリケーションの使用]: #console
[Mobile Services の使用]: #mobile-services
[アプリケーションを実行して通知を生成する]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Use Notification Hubs to send notifications to users (Notification Hubs を使用したユーザーへの通知の送信)]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store (方法: 通知ハブ (Windows ストア アプリ))]: http://msdn.microsoft.com/library/jj927172.aspx
[通知ハブ REST インターフェイス]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!--HONumber=45--> 
