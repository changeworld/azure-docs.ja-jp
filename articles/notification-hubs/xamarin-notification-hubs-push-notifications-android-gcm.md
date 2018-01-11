---
title: "Notification Hubs の使用 (Xamarin.Android アプリケーション) | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について学習します。"
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 4cb3aaa3d4e577e45f01f245d3898c033092f5a3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Notification Hubs の使用 (Xamarin.Android アプリケーション)
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について説明します。 Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Xamarin.Android アプリケーションを作成します。 完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。 完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、Notification Hubs を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

## <a name="before-you-begin"></a>開始する前に
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、GitHub の[こちら][GitHub]を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* Windows 上では [Visual Studio と Xamarin]。OS X 上では [Visual Studio for Mac]。
* アクティブな Google アカウント

このチュートリアルを完了することは、Xamarin.Android アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)を参照してください。
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Firebase Cloud Messaging を有効にする
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>通知ハブを構成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>上部にある <b>[構成]</b> タブを選択し、前のセクションで取得した <b>API キー</b>値を入力して、<b>[保存]</b> を選択します。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知ハブが FCM と連動するように構成されました。接続文字列により、アプリが通知を受信すると共に、プッシュ通知を送信するように登録されます。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
最初に、新しいプロジェクトを作成します。 

1. Visual Studio で **[新しいソリューション]** > **[Android アプリ]** を選択し、**[次へ]** を選択します。
   
      ![Visual Studio - 新しい Android プロジェクトを作成する][22]

2. **アプリケーション名**と**識別子**を入力します。 サポートする **[ターゲット プラットフォーム]** を選択し、**[次へ]**、**[作成]** の順に選択します。
   
      ![Visual Studio - Android アプリの構成][23]

    これにより、新しい Android プロジェクトが作成されます。

3. [ソリューション] ビューで新しいプロジェクトを右クリックして **[オプション]**を選択し、プロジェクトのプロパティを開きます。 **[Build (ビルド)]** セクションで **[Android Application (Android アプリケーション)]** 項目を選択します。
   
    **パッケージ名** の先頭の文字は、小文字にしてください。
   
   > [!IMPORTANT]
   > パッケージ名の先頭の文字は、小文字にする必要があります。 小文字にしないと、以下のプッシュ通知に登録するときに、アプリケーション マニフェスト エラーが発生します。
   > 
   > 
   
      ![Visual Studio - Android プロジェクトのオプション][24]
4. 必要に応じて、 **[Minimum Android version]** の API レベルを変更します。
5. 必要に応じて、**[対象の Android バージョン]** の API バージョンを変更します (API レベル 8 以上にする必要があります)。
6. **[OK]** を選択して、[Project Options]\(プロジェクト オプション\) ダイアログを閉じます。

### <a name="add-the-required-packages-to-your-project"></a>必要なパッケージをプロジェクトに追加します。

1. プロジェクトを右クリックし、**[追加]** > **[NuGet パッケージの追加]** を選択します。
2. **Xamarin.Azure.NotificationHubs.Android** を探し、プロジェクトに追加します。
3. **Xamarin.Firebase.Messaging** を探し、プロジェクトに追加します。

### <a name="set-up-notification-hubs-in-your-project"></a>プロジェクトで Notification Hubs を設定する
1. Android アプリケーションと通知ハブについて次の情報を収集します。
   
   * **接続文字列のリッスン**: [Azure Portal] のダッシュボードで **[接続文字列の表示]** をクリックします。 この値として接続文字列 *DefaultListenSharedAccessSignature* をコピーします。
   * **ハブ名**: [Azure Portal] から取得できるハブの名前です。 たとえば、 *mynotificationhub2*です。
     
2. Xamarin プロジェクトの **Constants.cs** クラスを作成し、このクラスに次の定数値を定義します。 プレースホルダーを実際の値に置き換えます。
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```

3. 次の using ステートメントを **MainActivity.cs**に追加します。
   
    ```csharp
        using Android.Util;
    ```

4. アプリを実行しているときにアラート ダイアログを表示するために使用する **MainActivity.cs** にインスタンス変数を追加します。
   
    ```csharp
        public const string TAG = "MainActivity";
    ```

5. **MainActivity.cs** の `base.OnCreate(savedInstanceState)` の後にある `OnCreate` に次のコードを追加します。

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```

6. プロジェクトを右クリックし、前述の Firebase プロジェクトからダウンロードした `google-services.json` を追加します。 追加されたファイルを右クリックし、ビルド アクションを `GoogleServicesJson` に設定します。

    ![Visual Studio - google-services.json を構成する][25]

7. **MyFirebaseIIDService** という新しいクラスを作成します。

8. 次の using ステートメントを **MyFirebaseIIDService.cs** に追加します。
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. **MyFirebaseIIDService.cs** で、**クラス**の宣言の上に次のコードを追加し、クラスが **FirebaseInstanceIdService** から継承するようにします。
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```

10. **MyFirebaseIIDService.cs** に次のコードを追加します。
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```

11. プロジェクトに別の新しいクラスを作成し、**MyFirebaseMessagingService** という名前を付けます。

12. 次の using ステートメントを **MyFirebaseMessagingService.cs** に追加します。
    
    ```csharp
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. クラスの宣言の上に次のコードを追加し、クラスが **FirebaseMessagingService** から継承するようにします。
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseIIDService : FirebaseMessagingService
    ```
    
14. 次のコードを **MyFirebaseMessagingService.cs** に追加します。
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

15. デバイスまたは読み込んだエミュレーター上でアプリを実行します

## <a name="send-notifications-from-the-portal"></a>ポータルから通知を送信する
アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 テスト プッシュ通知がデバイスに送信されます。

![Azure Portal - テスト送信][30]

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信できます。

通知の送信方法を確認できるチュートリアルの一覧を次に示します。

* ASP.NET: 「 [Notification Hubs を使用したユーザーへのプッシュ通知]」を参照してください。
* Azure Notification Hub Java SDK: Java からの通知を送信するには「 [Java から Notification Hubs を使用する方法](notification-hubs-java-push-notification-tutorial.md) 」を参照してください。 これは Android の開発用に Eclipse でテストされています。
* PHP: 「 [PHP から Notification Hubs を使用する方法](notification-hubs-php-push-notification-tutorial.md)」を参照してください。

## <a name="next-steps"></a>次の手順
この簡単な例では、すべての Android デバイスに通知をブロードキャストします。 特定のユーザーをターゲットとするには、「 [Notification Hubs を使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。 対象グループごとにユーザーを区分する場合は、「 [Notification Hubs を使用したニュース速報の送信]」を参照してください。 Notification Hubs の使用方法の詳細については、[Notification Hubs のガイダンス]に関するページと[Android 向けの Notification Hubs の手順]に関するページを参照してください。

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio と Xamarin]: https://docs.microsoft.com/en-us/visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs のガイダンス]: http://msdn.microsoft.com/library/jj927170.aspx
[Android 向けの Notification Hubs の手順]: http://msdn.microsoft.com/library/dn282661.aspx

[Notification Hubs を使用したユーザーへのプッシュ通知]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
