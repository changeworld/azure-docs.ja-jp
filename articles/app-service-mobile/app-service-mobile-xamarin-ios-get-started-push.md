<properties 
	pageTitle="Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を追加する" 
	description="Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を送信する方法について説明します" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="08/22/2015" 
	ms.author="wesmc"/>

# Xamarin iOS アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概要

このチュートリアルでは、[Xamarin.iOS クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。最初に、このチュートリアルの基になっている [Xamarin.iOS のクイック スタート] チュートリアルを完了しておく必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

[iOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)ため、物理的な iOS デバイスを使用する必要があります。さらに、[Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)にサインアップする必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手してください。このアプリは、評価終了後も使用できます。[Azure 無料評価版のサイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* [Xamarin Studio] と [Xcode] v4.4 以降がインストールされた Mac。Xamarin Studio を使用して、Mac で Xamarin.iOS アプリを実行するほうが簡単であることに注意してください。必要であれば Visual Studio を使用して Windows コンピューター上で Xamarin.iOS を実行できますが、ネットワーク化された Mac に接続する必要があるため、少し複雑になります。この設定に関心がある場合は、「[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]」をご覧ください。

* 物理的な iOS デバイス

* [クイック スタート チュートリアル](../app-service-mobile-xamarin-ios-get-started.md)を完了していること。

## <a id="register"></a>アプリをプッシュ通知用に登録する

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]

## プッシュ通知を送信するように Azure を構成する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>サーバー プロジェクトをプッシュ通知を送信するように更新する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>サーバー プロジェクトを Azure にデプロイする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a name="configure-app"></a>Xamarin.iOS プロジェクトを構成する

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push"></a>アプリケーションにプッシュ通知を追加する

1. **QSTodoService** で次のプロパティを追加して、**AppDelegate** でモバイル クライアントを取得できるようにします。
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. 次の `using` ステートメントを **AppDelegate.cs** ファイルの先頭に追加します。

        using Microsoft.WindowsAzure.MobileServices;

2. **AppDelegate** で、**FinishedLaunching** イベントをオーバーライドします。

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 同じファイルで **RegisteredForRemoteNotifications** イベントをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. 次に、**DidReceivedRemoteNotification** イベントをオーバーライドします。

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## <a name="test"></a>アプリケーションでプッシュ通知をテストする

1. **[Run]** ボタンを押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。
	
	> [AZURE.NOTE]アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

4. 手順 2. を繰り返してすぐにアプリケーションを閉じたら、通知が表示されることを確認します。

これで、このチュートリアルは終了です。

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=Nov15_HO1-->