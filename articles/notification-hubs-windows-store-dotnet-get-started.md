<properties urlDisplayName="Get started with notification hubs" pageTitle="Azure Notification Hubs の使用" metaKeywords="" description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して Windows ストアや Windows Phone 8.1 (Silverlight 以外) アプリケーションにプッシュ通知を送信する方法について説明します。Windows Phone 8.1 Silverlight を対象としている場合は、[Windows Phone](/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/) バージョンを参照してください。 
このチュートリアルでは、Windows プッシュ通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ストア アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する]
2. [通知ハブを構成する]
3. [通知ハブにアプリケーションを接続する]
4. [バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio Express 2013 for Windows with Update 2<br/>Visual Studio のこのバージョンでは、ユニバーサル アプリ プロジェクトを作成する必要があります。Windows ストア アプリのみを作成する場合は、Visual Studio 2012 Express for Windows 8 が必要です。

+ アクティブな Windows ストア アカウント

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

このチュートリアルを完了することは、Windows ストア アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。 

##<a name="register"></a>アプリケーションを Windows ストアに登録する

モバイル サービスから Windows ストア アプリにプッシュ通知を送信するには、アプリケーションを Windows ストアに提出する必要があります。さらに、WNS に統合するために通知ハブを構成する必要があります。

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで<a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">アプリ送信のページ</a>に移動し、Microsoft アカウントでログインして、**[アプリ名]** をクリックします。

   	![][0]

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリ名の予約]** をクリックして、**[保存]** をクリックします。

   	![][1]

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio で、**空のアプリケーション** テンプレートを使用して新しい Visual C# ストア アプリ プロジェクトを作成します。

   	![][2]

4. ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。 

   	![][3]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションをクリックし、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][4]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。 

7. (省略可能) Windows Phone ストア アプリ プロジェクトのステップ 4.～6. を繰り返します。  

7. 新しいアプリケーションに対応する Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。 

   	![][5] 

8. **[サービス]** ページで **[Microsoft Azure のモバイル サービス]** の **[ライブ サービス サイト]** をクリックします。

   	![][17]

9. **[アプリ設定]** タブで、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。 

   	![][6]

 	<div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
    </div>

##<a name="configure-hub"></a>通知ハブを構成する

1. [Azure の管理ポータル] にログオンし、画面下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は***通知ハブ名*-ns**) をクリックし、上部にある **[構成]** タブをクリックします。

   	![][9]

5. 上部にある **[通知ハブ]** を選択し、前の手順で作成した通知ハブをクリックします。

   	![][10]

6. 上部にある **[構成]** タブを選択し、前のセクションで WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力て、**[保存]** をクリックします。

   	![][11]

7.  上部にある **[ダッシュボード]** タブを選択し、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][12]

これで、通知ハブが WNS と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

##<a name="connecting-app"></a>通知ハブにアプリケーションを接続する

1. Visual Studio でソリューションを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

	[NuGet パッケージの管理] ダイアログ ボックスが表示されます。

2. `WindowsAzure.Messaging.Managed` を検索し、**[インストール]** をクリックして、ソリューション内のプロジェクトをすべて選択し、使用条件に同意します。

	![][20]

	これによりダウンロードとインストールが実行され、<a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>を使用して Windows 用 Azure メッセージング ライブラリへの参照がすべてのプロジェクトに追加されます。 

3. App.xaml.cs プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	ユニバーサル プロジェクトでは、このファイルは `<project_name>.Shared` フォルダーにあります。

4. さらに、App.xaml.cs で、次の **InitNotificationsAsync** メソッドの定義を **App** クラスに追加します。
	
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

    >[WACOM.NOTE]"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前 (たとえば、前の例では **mynotificationhub2**) に置き換えてください。また、接続文字列プレースホルダーを、前のセクションで取得した接続文字列 **DefaultListenSharedAccessSignature** に置き換えます。
    
5. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

    これにより、アプリケーションが起動されるたびに必ず ChannelURI が通知ハブに登録されます。

6. ソリューション エクスプローラーで Windows ストア アプリの **Package.appxmanifest** をダブルクリックし、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

   	![][18]

   	**[ファイル]** メニューから、**[すべて保存]** をクリックします。

7. (省略可能) Windows Phone ストア アプリ プロジェクトの前のステップを繰り返します。

8. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
   
   	![][19]

9. (省略可能) 前のステップを繰り返して、他のプロジェクトを実行します。

これで、アプリケーションがトースト通知を受信する準備が整いました。

##<a name="send"></a>バックエンドから通知を送信する

Notification Hubs を使用すれば、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからでも通知が送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**Mobile Services でのプッシュ通知の使用**」 ([.NET バックエンド](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/) | [JavaScript バックエンド](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)) を参照してください。REST API を使用して通知を送信する方法の例については、「**Java/PHP から Notification Hubs を使用する方法**」 ([Java](/ja-jp/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ja-jp/documentation/articles/notification-hubs-php-backend-how-to/)) を参照してください。

1. ソリューションを右クリックし、**[追加]**、**[新しいプロジェクト]** の順に選択します。次に、**[Visual C#]** で **[Windows]** および **[コンソール アプリケーション]** をクリックし、**[OK]** をクリックします。 

   	![][13]

	これにより、Visual C# の新しいコンソール アプリケーションがソリューションに追加されます。これは個別のソリューションで実行することもできます。 

4. 右クリックし、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 

	これで、パッケージ マネージャー コンソールが表示されます。

6. コンソール ウィンドウで **[既定のプロジェクト]** を新しいコンソール アプリケーション プロジェクトに設定した後、そのコンソール ウィンドウから次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus
    
	これで、<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure Service Bus SDK への参照が追加されます。 

5. Program.cs ファイルを開き、次の "using" ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

6. **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。 

	>[WACOM.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

7. 次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

8. スタートアップ プロジェクトとして設定されたコンソール アプリケーションで、**F5** キーを押してアプリケーションを実行します。 

   	![][14]

	登録したすべてのデバイスがトースト通知を受信します。トースト バナーをクリックまたはタップすると、アプリを読み込みます。

MSDN の[トースト カタログ]、[タイル カタログ]、および[バッジの概要]のトピックに、使用できるすべてのペイロードが記載されています。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Windows デバイスにブロードキャスト通知を送信します。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。Notification Hubs の使用方法の詳細については、「[Notification Hubs の概要]」を参照してください。

<!-- Anchors. -->
[アプリケーションをプッシュ通知に登録する]: #register
[通知ハブを構成する]: #configure-hub
[通知ハブにアプリケーションを接続する]: #connecting-app
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
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
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-dotnet
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx

[通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet

[トースト カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761494.aspx
[タイル カタログ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh761491.aspx
[バッジの概要]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh779719.aspx
