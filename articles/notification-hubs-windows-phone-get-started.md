<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="作業の開始" pageTitle="Azure 通知ハブの使用" metaKeywords="" description="Azure 通知ハブを使用してプッシュ通知を行う方法について説明します。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="通知ハブの使用" authors=""  solutions="" writer="elioda" manager="" editor=""  />
# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows ストア C#">Windows ストア C##</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して Windows Phone 8 アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用してプッシュ通知を受信する空の Windows Phone 8 アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の手順について説明します。

1. [通知ハブを作成する]
2. [通知ハブにアプリケーションを接続する]
3. [バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルには、次のものが必要です。

+ [Visual Studio 2012 Express for Windows Phone] 以降のバージョン

このチュートリアルを完了することは、Windows Phone 8 アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>メモ</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>


<h2><a name="configure-hub"></a><span class="short-header">通知ハブの作成</span>通知ハブを作成する</h2>

1. [Azure の管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

   	![][9]

5. 上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   	![][10]

6. 下部にある **[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][12]

7. **[構成]** タブをクリックし、**[Windows Phone 通知設定]** セクションの **[非認証プッシュ通知を有効にする]** チェック ボックスをオンにします。

   	![][15]

これで、Windows Phone 8 アプリケーションを登録して通知を送信するのに必要な接続文字列が取得されました。

<div class="dev-callout"><b>メモ</b>
		<p>このチュートリアルでは、非認証モードで MPNS を使用します。MPNS 非認証モードでは、各チャネルに送信できる通知に制限があります。通知ハブのサポート<a href="http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx">MPNS 認証モード</a>。<!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

<h2><a name="connecting-app"></a><span class="short-header">アプリケーションを接続する</span>通知ハブにアプリケーションを接続する</h2>

1. Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。

   	![][13]

1. <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>を使用して、Windows ストアの Azure メッセージング ライブラリに参照を追加します。Visual Studio のメニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.Messaging.Managed

    次に、Enter キーを押します。

2. App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

3. App.xaml.cs 内にある **Application_Launching** メソッドの先頭に、次のコードを追加します。
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    必ず、ハブの名前と、前のセクションで取得した **DefaultListenSharedAccessSignature** という接続文字列を挿入してください。
    このコードにより、MPNS からアプリケーションの ChannelURI が取得され、その ChannelURI が通知ハブに登録されます。これにより、アプリケーションが起動されるたびに必ず ChannelURI も通知ハブに登録されます。

	<div class="dev-callout"><b>メモ</b>
		<p>このチュートリアルでは、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの <strong>BindToShellTile</strong> メソッドを呼び出す必要があります。トースト通知とタイル通知の両方をサポートするには、<strong>BindToShellTile</strong> と <strong>BindToShellToast</strong> の両方を呼び出します。</p>
	</div>
    
4. ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

   	![][14]

   	これにより、アプリケーションでプッシュ通知の受信が有効になります。
	
5. F5 キーを押してアプリケーションを実行します。

<h2><a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する</h2>

通知は、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST</a> インターフェイスを使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1. Visual C# の新しいコンソール アプリケーションを作成します。

   	![][213]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure の Service Bus SDK に参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus

    次に、Enter キーを押します。

2. Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

3.`Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	必ず、ハブの名前と、「通知ハブを構成する」で取得した DefaultFullSharedAccessSignature という接続文字列を挿入してください。これは、リッスン アクセスではなくフル アクセスを持つ接続文字列である点に注意してください。


4. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 キーを押してアプリケーションを実行します。トースト通知を受信します。必ず、Windows Phone エミュレーターを実行し、アプリケーションを閉じてください。

MSDN の[トースト カタログ]および[タイル カタログ]に、使用できるすべてのペイロードが記載されています。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Windows Phone 8 デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Azure 通知ハブの概要]」を参照してください。

<!-- Anchors. -->
[通知ハブを作成する]: #configure-hub
[通知ハブにアプリケーションを接続する]: #connecting-app
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->







[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png

[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png

[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service

[Azure の管理ポータル]: https://manage.windowsazure.com/
[Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (Windows Phone 8 アプリ) に関するページ]: tbd!!!
[MPNS 認証モード]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx
[通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[トースト カタログ]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/jj662938(v=vs.105).aspx
[タイル カタログ]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/hh202948(v=vs.105).aspx

