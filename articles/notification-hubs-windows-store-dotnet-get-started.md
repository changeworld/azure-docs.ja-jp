<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="通知ハブの使用" pageTitle="Azure 通知ハブの使用" metaKeywords="" description="Azure 通知ハブを使用してプッシュ通知を行う方法について説明します。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="通知ハブの使用" authors=""  solutions="" writer="elioda" manager="" editor=""  />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jpus/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して Windows ストア アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Windows プッシュ通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ストア アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する]
2. [通知ハブを構成する]
3. [通知ハブにアプリケーションを接続する]
4. [バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows 8
+ アクティブな Windows ストア アカウント

このチュートリアルを完了することは、Windows ストア アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>メモ</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<h2><a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションを Windows ストアに登録する</h2>

モバイル サービスから Windows ストア アプリにプッシュ通知を送信するには、アプリケーションを Windows ストアに提出する必要があります。さらに、WNS に統合するために通知ハブを構成する必要があります。

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリ送信のページ]に移動し、Microsoft アカウントでログインして、**[アプリ名]** をクリックします。

   	![][0]

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリ名の予約]** をクリックして、**[保存]** をクリックします。

   	![][1]

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio 2012 Express for Windows 8 で、**空のアプリケーション** テンプレートを使用して新しい Visual C# Windows ストア プロジェクトを作成します。

   	![][2]

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

   	![][3]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションをクリックし、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][4]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

   	![][5] 

8. **[サービス]** ページで **[Azure のモバイル サービス]** の **[ライブ サービス サイト]** をクリックします。

   	![][17]

9. **[サービスの認証]** をクリックし、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

   	![][6]

 	<div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
    </div>

<h2><a name="configure-hub"></a><span class="short-header">通知ハブを構成する</span>通知ハブを構成する</h2>

1. [Azure の管理ポータル]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

   	![][9]

5. 上部にある **[通知ハブ]** を選択し、前の手順で作成した通知ハブをクリックします。

   	![][10]

6. 上部にある **[構成]** タブを選択し、前のセクションで WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力て、**[保存]** をクリックします。

   	![][11]

7. 上部にある **[ダッシュボード]** タブを選択し、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][12]

これで、通知ハブが WNS と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

<h2><a name="connecting-app"></a><span class="short-header">アプリケーションを接続する</span>通知ハブにアプリケーションを接続する</h2>

1. <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>を使用して、Windows ストアの Azure メッセージング ライブラリに参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.Messaging.Managed

    次に、**Enter** キーを押します。

2. App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

3. App.xaml.cs の `App` クラス内に、次のコードを追加します。"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前 (たとえば、前の例では **mynotificationhub2**) に置き換えてください。
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    必ず、ハブの名前と、前のセクションで取得した **DefaultListenSharedAccessSignature** という接続文字列を挿入してください。
    このコードにより、MPNS からアプリケーションの ChannelURI が取得され、その ChannelURI が通知ハブに登録されます。
    
4. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

    これにより、アプリケーションが起動されるたびに必ず ChannelURI が通知ハブに登録されます。

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
   
   	![][19]

<h2><a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する</h2>

通知は、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST</a> インターフェイスを使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1. Visual Studio で前のソリューションがまだ開いている場合は、ソリューション エクスプローラーで **Package.appxmanifest** をダブルクリックして Visual Studio エディターで開きます。

2. **[すべてのイメージ資産]** まで下へスクロールして、**[バッジ ロゴ]** をクリックします。**[通知]** で、**[トースト対応]** を **[はい]** に設定します。

   	![][18]

   	**[ファイル]** メニューの **[すべて保存]** をクリックします。

3. Visual C# の新しいコンソール アプリケーションを作成します。

   	![][13]

4. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure の Service Bus SDK に参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus

    次に、**Enter** キーを押します。

5. Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

6. `Program` クラスで、次のメソッドを追加します。"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	必ず、ハブの名前と、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列を挿入してください。これは、**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列である点に注意してください。

7. 次に、`Main` メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

8. **F5** キーを押してアプリケーションを実行します。トースト通知を受信します。

   	![][14]

MSDN の[トースト カタログ]、[タイル カタログ]、および[バッジの概要]に、使用できるすべてのペイロードが記載されています。

モバイル サービスを使用して通知を送信するには、「[モバイル サービスの使用]」に従った後、次の手順を実行します。

1. [Azure の管理ポータル]にログオンし、[モバイル サービス] をクリックします。

2. 上部にある **[スケジューラ]** タブを選択します。

   	![][15]

3. スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

   	![][16]

4. ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5. スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。完了したら、下部のバーにある **[保存]** をクリックします。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6. 下部のバーにある **[一度だけ実行する]** をクリックします。トースト通知を受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Windows デバイスにブロードキャスト通知を送信します。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Azure 通知ハブの概要]」と「[方法: Azure 通知ハブ (Windows ストア アプリ)]」を参照してください。

<!-- Anchors. -->
[アプリケーションをプッシュ通知に登録する]: #register
[通知ハブを構成する]: #configure-hub
[通知ハブにアプリケーションを接続する]: #connecting-app
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png

<!-- URLs. -->
[アプリ送信のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-dotnet
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js

[Azure の管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (Windows ストア アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927172.aspx

[通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet

[トースト カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761494.aspx
[タイル カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761491.aspx
[バッジの概要]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh779719.aspx

