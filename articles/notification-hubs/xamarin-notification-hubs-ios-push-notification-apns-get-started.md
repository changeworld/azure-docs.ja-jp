---
title: "Azure Notification Hubs の使用 (Xamarin.iOS アプリケーション) | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.iOS アプリケーションにプッシュ通知を送信する方法について学習します。"
services: notification-hubs
keywords: "ios push notifications、プッシュ メッセージ、プッシュ通知、プッシュ メッセージ"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Azure Notification Hubs の使用 (Xamarin iOS アプリケーション)
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)を参照してください。
> 
> 

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。 [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) を使用してプッシュ通知を受信する空の Xamarin.iOS アプリケーションを作成します。 

完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。 完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、Notification Hubs を使用した簡単なプッシュ メッセージ ブロードキャスト シナリオのデモンストレーションを行います。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* 最新バージョンの [XCode][Install Xcode]
* iOS 10 以降のバージョンに対応するデバイス
* [Apple Developer Program](https://developer.apple.com/programs/) メンバーシップ
* [Visual Studio for Mac]
  
  > [!NOTE]
  > iOS Push Notifications の構成要件に従って、サンプル アプリケーションのデプロイとテストは、シミュレーターではなく、物理的な iOS デバイス (iPhone または iPad) で行う必要があります。
  > 
  > 

このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS プッシュ通知向けに通知ハブを構成する
このセクションでは、以前に作成した **.p12** プッシュ証明書を使用して、新しい通知ハブを作成し、APNS での認証を構成する手順について説明します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p><b>[Notification Services]</b> ボタンをクリックし、<b>[Apple (APNS)]</b> を選択します。 <b>[証明書]</b> を選択するには、ファイル アイコンをクリックし、以前にエクスポートした <b>.p12</b> ファイルを選択します。 必ず正しいパスワードも入力してください。</p>

<p>これは開発用であるため、<b>[サンドボックス]</b> モードを選択してください。 <b>[実稼働]</b> は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Azure Portal で APNS を構成する][6]

&emsp;&emsp;&emsp;&emsp;![Azure Portal で APNS 証明書を構成する][7]

これで、通知ハブが APNS と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
#### <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. Visual Studio で、新しい iOS プロジェクトを作成し、**[単一ビュー アプリ]** テンプレートを選択し、**[次へ]** をクリックします。
   
     ![Visual Studio - アプリケーションの種類を選択する][31]

2. アプリケーション名と組織の識別子を入力し、**[次へ]**、**[作成]** の順にクリックします。

3. ソリューション ビューの *Into.plist* をクリックし、**[ID]** で、バンドル識別子がプロビジョニング プロファイルの作成時に使用した識別子と一致することを確認します。 **[署名]** で、**[チーム]** に Developer アカウントを選択し、[Automatically manage signing]\(署名を自動的に管理する\) をオンにし、署名証明書とプロビジョニング プロファイルが自動的に選択されることを確認します。

    ![Visual Studio - iOS アプリの構成][32]

4. Azure Messaging パッケージを追加します。 ソリューション ビューでプロジェクトを右クリックし、**[追加]** > **[NuGet パッケージの追加]** を選択します。 **Xamarin.Azure.NotificationHubs.iOS** を検索し、パッケージをプロジェクトに追加します。

5. 新しいファイルをクラスに追加し、**Constants.cs** と名前を付け、次の変数を追加し、文字列リテラルのプレースホルダーを*ハブ名*とメモしておいた *DefaultListenSharedAccessSignature* に置き換えます。
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. **AppDelegate.cs**で、次の using ステートメントを追加します。
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. **SBNotificationHub**のインスタンスを宣言します。
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. **AppDelegate.cs** で、**FinishedLaunching()** を更新して次の内容に合わせます。
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > ネットワーク接続がないなどの状況に対処するために、 **FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。 これは、ユーザーがオフラインモード (Airplane など) でアプリケーションを起動し、アプリケーション固有のプッシュメッセージングシナリオを処理する場合に特に重要です。
  

12. デバイスでアプリケーションを実行します。

## <a name="sending-test-push-notifications"></a>テストプッシュ通知の送信
アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 テスト プッシュ通知がデバイスに送信されます。

![Azure Portal - テスト送信][30]

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信できます。

ASP.NET バックエンドから通知を送信するには、次の手順として、[Notification Hubs を使用したユーザーへのプッシュ通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)に関するチュートリアルを参照することをお勧めします。 ただし、通知の送信には、次の方法も使用できます。

通知の送信方法を確認できるチュートリアルの一覧を次に示します。
* REST インターフェイス: [REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用すると、任意のバックエンド プラットフォームのプッシュ通知をサポートできます。
* **Microsoft Azure Notification Hubs .NET SDK**: Nuget Package Manager for Visual Studio で、 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)を実行します。
* Node.js: [Node.js から Notification Hubs を使用する方法](notification-hubs-nodejs-push-notification-tutorial.md)。
* Java/PHP**: REST API を使用したプッシュ通知の送信方法の例については、「Java/PHP から Notification Hubs を使用する方法」([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) を参照してください。

## <a name="next-steps"></a>次の手順
この簡単な例では、すべての iOS デバイスにプッシュ通知をブロードキャストします。 特定のユーザーをターゲットとするには、「 [Notification Hubs を使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。 対象グループごとにユーザーを区分する場合は、「 [Notification Hubs を使用したニュース速報の送信]」を参照してください。 Notification Hubs の使用方法の詳細については、「[Notification Hubs の概要]」と [iOS 向けの Notification Hubs の使用方法]に関する記事を参照してください。

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 向けの Notification Hubs の使用方法]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Notification Hubs を使用したユーザーへのプッシュ通知]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
