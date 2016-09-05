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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Xamarin iOS アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Overview

最初に、このチュートリアルの基になっている [Xamarin.iOS のクイックスタート](app-service-mobile-xamarin-ios-get-started.md)に関するチュートリアルを完了しておく必要があります。Xamarin.iOS クイック スタート プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##前提条件

* [Xamarin.iOS のクイックスタート](app-service-mobile-xamarin-ios-get-started.md)に関するチュートリアルを完了していること。

* 物理的な iOS デバイスiOS シミュレーターでは、プッシュ通知はサポートされていません。

##Apple の開発者ポータルにプッシュ通知のアプリを登録する

[AZURE.INCLUDE [Notification Hubs Xamarin で Apple プッシュ通知を有効にする](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##プッシュ通知を送信するようにモバイル アプリを構成する

通知を送信するようにアプリを構成するには、新しいハイブリッドを作成し、使用するプラットフォームの通知サービスに合わせて構成します。

1. [Azure ポータル](https://portal.azure.com/)で、**[参照]**、**[Mobile Apps]**、作成したモバイル アプリ、**[設定]**、**[モバイル]**、**[プッシュ]**、**[通知ハブ]**、**[+ 通知ハブ]** の順にクリックし、新しい通知ハブの名前と名前空間を指定してから、**[OK]** をクリックします。

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. [通知ハブの作成] ブレードで、**[作成]** をクリックします。

3. **[プッシュ]**、**[Apple (APNs)]**、**[証明書のアップロード]** の順にクリックします。以前にエクスポートした .p12 プッシュ証明書ファイルをアップロードします。開発とテスト用に開発プッシュ証明書を作成した場合は、**[サンドボックス]** を選択します。それ以外の場合は、**[運用]** を選択します。

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

##プッシュ通知を送信するようにサーバー プロジェクトを更新する

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Xamarin.iOS プロジェクトを構成する

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##アプリケーションにプッシュ通知を追加する

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
		using Newtonsoft.Json.Linq;

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

3. 同じファイルで **RegisteredForRemoteNotifications** イベントをオーバーライドします。このコードでは、サーバーでサポートされているすべてのプラットフォームに送信される単純なテンプレート通知を登録します。

	Notification Hubs を使用するテンプレートの詳細については、「[テンプレート](../notification-hubs/notification-hubs-templates.md)」を参照してください。


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
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

	> [AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

4. 手順 2. を繰り返してすぐにアプリケーションを閉じたら、通知が表示されることを確認します。

これで、このチュートリアルは終了です。

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0824_2016-->