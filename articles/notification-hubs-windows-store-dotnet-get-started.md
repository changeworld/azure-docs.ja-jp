<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル" class="current">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して Windows ストアや Windows Phone 8.1 (Silverlight 以外) アプリケーションにプッシュ通知を送信する方法について説明します。Windows Phone 8.1 Silverlight を対象とする場合は、[Windows Phone][1] バージョンを参照してください。
このチュートリアルでは、Windows プッシュ通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ストア アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションをプッシュ通知に登録する][アプリケーションをプッシュ通知に登録する]
2.  [通知ハブを構成する][通知ハブを構成する]
3.  [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
4.  [バックエンドから通知を送信する][バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルには、次のものが必要です。

-   Microsoft Visual Studio Express 2013 for Windows Update 2
    Visual Studio のこのバージョンでは、ユニバーサル アプリ プロジェクトを作成する必要があります。Windows ストア アプリのみを作成する場合は、Visual Studio 2012 Express for Windows 8 が必要です。

-   アクティブな Windows ストア アカウント

-   アクティブな Azure アカウント
    アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

このチュートリアルを完了することは、Windows ストア アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

## <a name="register"></a>アプリケーションを Windows ストアに登録する

モバイル サービスから Windows ストア アプリにプッシュ通知を送信するには、アプリケーションを Windows ストアに提出する必要があります。さらに、WNS に統合するために通知ハブを構成する必要があります。

1.  アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ][アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

    ![][]

2.  **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

    ![][2]

    これでアプリケーションの新しい Windows ストア登録が作成されます。

3.  Visual Studio で、**空のアプリケーション** テンプレートを使用して新しい Visual C# ストア アプリ プロジェクトを作成します。

    ![][3]

4.  ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][4]

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5.  ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6.  ステップ 2. で登録したアプリケーションをクリックし、**[次へ]**、**[関連付け]** の順にクリックします。

    ![][5]

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7.  (省略可能) Windows Phone ストア アプリ プロジェクトのステップ 4.～6. を繰り返します。

8.  新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

    ![][6]

9.  **[サービス]** ページで **[Microsoft Azure のモバイル サービス]** の **[ライブ サービス サイト]** をクリックします。

    ![][7]

10. **[アプリ設定]** タブで、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

    ![][8]

    <div class="dev-callout">

    <b>セキュリティに関する注意</b>
    <p>クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>

    </div>

## <a name="configure-hub"></a>通知ハブを構成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][9]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][10]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

    ![][11]

5.  上部にある **[通知ハブ]** を選択し、前の手順で作成した通知ハブをクリックします。

    ![][12]

6.  上部にある **[構成]** タブを選択し、前のセクションで WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

    ![][13]

7.  上部にある **[ダッシュボード]** タブを選択し、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

    ![][14]

これで、通知ハブが WNS と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

## <a name="connecting-app"></a>通知ハブにアプリケーションを接続する

1.  Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

    [NuGet パッケージの管理] ダイアログ ボックスが開きます。

2.  `WindowsAzure.Messaging.Managed` を検索し、**[インストール]** をクリックし、ソリューション内のすべてのプロジェクトを選択して、使用条件に同意します。

    ![][15]

    こうすることで、[WindowsAzure.Messaging.Managed NuGet パッケージ][WindowsAzure.Messaging.Managed NuGet パッケージ]を使用して、Windows の Azure メッセージング ライブラリにすべてのプロジェクトの参照がダウンロード、インストール、追加されます。

3.  App.xaml.cs プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    ユニバーサル プロジェクトでは、このファイルは `<project_name>.Shared` フォルダーにあります。

4.  さらに、App.xaml.cs で、次の **InitNotificationsAsync** メソッドの定義を **App** クラスに追加します。

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

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が通知ハブに登録されます。

    > [WACOM.NOTE]"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前 (たとえば、前の例では **mynotificationhub2**) に置き換えてください。また、接続文字列プレースホルダーを、先に取得した **DefaultListenSharedAccessSignature** 接続文字列に置き換えます。

5.  App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

    これにより、アプリケーションが起動されるたびに必ず ChannelURI が通知ハブに登録されます。

6.  ソリューション エクスプローラーで Windows ストア アプリの **Package.appxmanifest** をダブルクリックし、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

    ![][16]

    **[ファイル]** メニューの **[すべて保存]** をクリックします。

7.  (省略可能) Windows Phone ストア アプリ プロジェクトの前のステップを繰り返します。

8.  **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。

    ![][17]

9.  (省略可能) 前のステップを繰り返して、他のプロジェクトを実行します。

これで、アプリケーションがトースト通知を受信する準備が整いました。

## <a name="send"></a>バックエンドから通知を送信する

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。通知ハブと統合した Azure モバイル サービス バックエンドからの通知の送信例については、「**モバイル サービスでのプッシュ通知の使用**」([.NET バックエンド][.NET バックエンド] | [JavaScript バックエンド][.NET バックエンド]) を参照してください。REST API を使用した通知の送信方法の例については、「**How to use Notification Hubs from Java/PHP (Java/PHP から Notification Hubs を使用する方法)**」([Java][Java] | [PHP][PHP]) を参照してください。

1.  ソリューションを右クリックし、**[追加]** と **[新しいプロジェクト]** を選択し、**[Visual C#]** で **[Windows]** と **[コンソール アプリケーション]** をクリックして、**[OK]** をクリックします。

    ![][18]

    これにより、新しい Visual C# コンソール アプリケーションがソリューションに追加されます。個別のソリューションでこの操作を行うこともできます。

2.  右クリックし、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

    これにより、パッケージ マネージャー コンソールが開きます。

3.  コンソール ウィンドウで、**[既定のプロジェクト]** を新しいコンソール アプリケーション プロジェクトに設定し、次に、コンソール ウィンドウで次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus

    これにより、[WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure のサービス バス SDK に参照を追加します。

4.  Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5.  **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    "hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列プレースホルダーを、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えます。

    > [WACOM.NOTE]これは、**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列である点に注意してください。リッスン アクセス文字列には通知を送信するアクセス許可はありません。

6.  次に、**Main** メソッド内に、次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

7.  スタートアップ プロジェクトとして設定されたコンソール アプリケーションで、**F5** キーを押してアプリケーションを実行します。

    ![][19]

    登録したすべてのデバイスでトースト通知を受信します。トースト バナーをクリックまたはタップすると、アプリを読み込みます。

MSDN の [トースト カタログ][トースト カタログ]、[タイル カタログ][タイル カタログ]、[バッジの概要][バッジの概要]トピックに、サポートされるすべてのペイロードが記載されています。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Windows デバイスにブロードキャスト通知を送信します。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[通知ハブの概要][通知ハブの概要]」を参照してください。



  [Windows ユニバーサル]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows ユニバーサル"
  [Windows Phone]: /ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ja-jp/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ja-jp/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/
  [アプリケーションをプッシュ通知に登録する]: #register
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [バックエンドから通知を送信する]: #send
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [WindowsAzure.Messaging.Managed NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /ja-jp/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ja-jp/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [トースト カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761494.aspx
  [タイル カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761491.aspx
  [バッジの概要]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh779719.aspx
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
