<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。Windows Phone 8.1 (Silverlight 以外) を対象としている場合は、[Windows ユニバーサル][1] バージョンを参照してください。
このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用してプッシュ通知を受信する空の Windows Phone 8 アプリを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

> [AZURE.NOTE] Notification Hubs Windows Phone SDK では、Windows Phone 8.1 Silverlight アプリで WNS を使用することはできません。Windows Phone 8.1 Silverlight アプリで (MPNS ではなく) WNS を使用するには、「[Windows ユニバーサルの使用][1]」に示すように WNS の資格情報を設定する必要があります。その後、[ユーザーへの通知][ユーザーへの通知]チュートリアルに示すようにバックエンドから登録するか、[Notification Hubs REST API][Notification Hubs REST API] を使用できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の手順について説明します。

1.  [通知ハブを作成する][通知ハブを作成する]
2.  [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
3.  [バックエンドから通知を送信する][バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルには、次のものが必要です。

-   [Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] 以降のバージョン

このチュートリアルを完了することは、Windows Phone 8 アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="configure-hub"></a>通知ハブを作成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2.  **[アプリ サービス]**、**[Service Bus]**、**[Notification Hub]**、**[簡易作成]** の順にクリックします。

    ![][0]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][2]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

    ![][3]

5.  上部にある **[Notification Hubs]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

    ![][4]

6.  下部にある **[接続情報]** をクリックします。2 つの接続文字列をメモします。

    ![][5]

7.  **[構成]** タブをクリックし、**[Windows Phone 通知設定]** セクションの **[非認証プッシュ通知を有効にする]** チェック ボックスをオンにします。

    ![][6]

これで、Windows Phone 8 アプリケーションを登録して通知を送信するのに必要な接続文字列が取得されました。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、非認証モードで MPNS を使用します。MPNS 非認証モードでは、各チャネルに送信できる通知に制限があります。通知ハブのサポート<a href="http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx">MPNS 認証モード</a>。 <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>通知ハブにアプリケーションを接続する

1.  Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。

    ![][7]

    Visual Studio 2013 Update 2 以降では、代わりに Windows Phone Silverlight アプリケーションを作成します。

    ![][8]

2.  Visual Studio でソリューションを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    [NuGet パッケージの管理] ダイアログ ボックスが表示されます。

3.  `WindowsAzure.Messaging.Managed` を検索し、**[インストール]** をクリックして、使用条件に同意します。

    ![][9]

    これによりパッケージのダウンロードとインストールが実行され、[WindowsAzure.Messaging.Managed NuGet パッケージ][WindowsAzure.Messaging.Managed NuGet パッケージ]を使用して、Windows の Azure メッセージング ライブラリに参照が追加されます。

4.  App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  App.xaml.cs 内の **Application\_Launching** メソッドの先頭に、次のコードを追加します。

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
    このコードにより、MPNS からアプリの ChannelURI が取得され、その ChannelURI が通知ハブに登録されます。これにより、アプリケーションが起動するたびに必ず ChannelURI も通知ハブに登録されます。

    > [WACOM.NOTE] このチュートリアルでは、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。トースト通知とタイル通知の両方をサポートするには、**BindToShellTile** と **BindToShellToast** の両方を呼び出します。

6.  ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

    ![][10]

    これにより、アプリケーションでプッシュ通知の受信が有効になります。

7.  F5 キーを押してアプリケーションを実行します。

    登録メッセージが表示されます。

## <a name="send"></a>バックエンドから通知を送信する

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**Mobile Services でのプッシュ通知の使用**」 ([.NET バックエンド][.NET バックエンド] | [JavaScript バックエンド][.NET バックエンド]) を参照してください。REST API を使用して通知を送信する方法の例については、「**Java/PHP から Notification Hubs を使用する方法**」 ([Java][Java] | [PHP][PHP]) を参照してください。

1.  ソリューションを右クリックし、**[追加]**、**[新しいプロジェクト]** の順に選択します。次に、**[Visual C#]** で、**[Windows]** および **[コンソール アプリケーション]** をクリックし、**[OK]** をクリックします。

    ![][11]

    これにより、Visual C# の新しいコンソール アプリケーションがソリューションに追加されます。これは個別のソリューションで実行することもできます。

2.  右クリックし、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

    これで、パッケージ マネージャー コンソールが表示されます。

3.  コンソール ウィンドウで **[既定のプロジェクト]** を新しいコンソール アプリケーション プロジェクトに設定した後、そのコンソール ウィンドウから次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus

    これで、[WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK への参照が追加されます。

4.  Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5.  **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    "hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。

    > [WACOM.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

6.  次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

7.  Windows Phone エミュレーターを実行し、アプリを閉じて、コンソール アプリケーション プロジェクトを既定のスタートアップ プロジェクトとして設定します。次に、F5 キーを押して、アプリを実行します。

    トースト通知を受信します。トースト バナーをタップすると、アプリが読み込まれます。

MSDN の[トースト カタログ][トースト カタログ]および[タイル カタログ][タイル カタログ]に関するトピックに、使用できるすべてのペイロードが記載されています。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Windows Phone 8 デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要][Windows Azure 通知ハブの概要]」を参照してください。

 
 


  [Windows ユニバーサル]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows ユニバーサル"
  [1]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [ユーザーへの通知]: /ja-jp/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [Notification Hubs REST API]: http://msdn.microsoft.com/ja-jp/library/dn223264.aspx
  [通知ハブを作成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [バックエンドから通知を送信する]: #send
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [WindowsAzure.Messaging.Managed NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /ja-jp/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ja-jp/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [トースト カタログ]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [タイル カタログ]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
