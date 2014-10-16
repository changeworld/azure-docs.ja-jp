このセクションでは、.NET コンソール アプリなどから通知を送信する方法について説明します。
Mobile Services を使用する場合は、[プッシュ通知の使用][]チュートリアルを参照してください。Java または PHP を使用する場合は、「[How to use Notification Hubs from Java/PHP (Java/PHP から Notification Hubs を使用する方法)][]」を参照してください。[通知ハブ REST インターフェイス][]を使用することで、バックエンドから通知を送信できます。

次のコードでは、Windows ストア デバイス、Windows Phone デバイス、iOS デバイス、Android デバイスに通知を送信します。

「[Get started with Notification Hubs (通知ハブの使用)][]」を実行したときにコンソール アプリケーションを作成した場合は、ステップ 1. ～ 3. はスキップします。

1.  Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。

    ![][]

2.  Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックし、コンソール ウィンドウで次のコマンドを入力して、**Enter** キーを押します。

        Install-Package WindowsAzure.ServiceBus

    これにより、[WindowsAzure.ServiceBus NuGet パッケージ][]を使用して Azure Service Bus SDK への参照が追加されます。

3.  Program.cs ファイルを開き、次の `using` ステートメントを追加します。

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

    <div class="dev-callout"><strong>注</strong> 
<p>このバックエンド コードは、Windows ストア クライアント、Windows Phone クライアント、iOS クライアント、Android クライアントをサポートします。send メソッドは、通知ハブが特定のクライアント プラットフォームに対してまだ構成されていない場合、エラー応答を返します。 </p>
</div>

5.  上のコード内で、`<hub name>` および `<connection string with full access>` プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えます。

6.  **Main** メソッド内に、次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors --> 
<!-- Images. --> 
<!-- URLs. -->

  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [How to use Notification Hubs from Java/PHP (Java/PHP から Notification Hubs を使用する方法)]: /ja-jp/documentation/articles/notification-hubs-java-backend-how-to/
  [通知ハブ REST インターフェイス]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [Get started with Notification Hubs (通知ハブの使用)]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
