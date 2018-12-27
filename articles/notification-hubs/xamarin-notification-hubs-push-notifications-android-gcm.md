---
title: Azure Notification Hubs を使用して Xamarin.Android アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について学習します。
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 94e8e813b537d304e62854b81979d433d0645115
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918805"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して Xamarin.Android アプリにプッシュ通知を送信する
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について説明します。 Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Xamarin.Android アプリを作成します。 通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストします。 完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Firebase プロジェクトを作成し、Firebase Cloud Messaging を有効にする
> * 通知ハブを作成する
> * Xamarin.Android アプリを作成し、それを通知ハブに接続する
> * Azure Portal からテスト通知を送信する

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- Windows 上では [Visual Studio と Xamarin]。OS X 上では [Visual Studio for Mac]。
- アクティブな Google アカウント

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Firebase プロジェクトを作成し、Firebase Cloud Messaging を有効にする
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>通知ハブを作成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>GCM の設定を通知ハブ用に構成する

1. **[通知設定]** セクションで、**[Google (GCM)]** を選択します。 
2. Google Firebase Console からメモした**レガシ サーバー キー**を入力します。 
3. ツールバーの **[保存]** を選択します。 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知ハブが FCM と連動するように構成されました。接続文字列により、アプリが通知を受信すると共に、プッシュ通知を送信するように登録されます。

## <a name="create-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Xamarin.Android アプリを作成し、それを通知ハブに接続する

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio プロジェクトを作成し、NuGet パッケージを追加する
1. Visual Studio で、**[ファイル]** をポイントし、**[新規]** を選択してから **[プロジェクト]** を選択します。 
   
      ![Visual Studio - 新しい Android プロジェクトを作成する](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. **ソリューション エクスプローラー** ウィンドウで、**[プロパティ]** を展開し、**[AndroidManifest.xml]** をクリックします。 Google Firebase Console でプロジェクトに Firebase Cloud Messaging を追加するときに入力したパッケージ名に一致するようにパッケージ名を更新します。 

      ![GCM でのパッケージ名](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. プロジェクトを右クリックし、**[NuGet パッケージの管理...]** を選択します。 
4. **[参照]** タブを選択します。**Xamarin.GooglePlayServices.Base** を検索します。 結果の一覧で **[Xamarin.GooglePlayServices.Base]** を選択します。 その後、**[インストール]** を選択します。 

      ![Google Play 開発者サービス NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. **[NuGet パッケージ マネージャー]** ウィンドウで、**Xamarin.Firebase.Messaging** を検索します。 結果の一覧で **[Xamarin.Firebase.Messaging]** を選択します。 その後、**[インストール]** を選択します。 
6. 次に、**Xamarin.Azure.NotificationHubs.Android** を検索します。 結果の一覧で **[Xamarin.Azure.NotificationHubs.Android]** を選択します。 その後、**[インストール]** を選択します。 

### <a name="add-the-google-services-json-file"></a>Google Services JSON ファイルを追加する

1. Google Firebase Console からダウンロードした **google-services.json** をプロジェクト フォルダーにコピーします。
2. **google-services.json** をプロジェクトに追加します。
3. **ソリューション エクスプローラー** ウィンドウで、**[google-services.json]** を選択します。
4. **[プロパティ]** ウィンドウで、ビルド アクションを **[GoogleServicesJson]** に設定します。 **[GoogleServicesJson]** が表示されない場合は、Visual Studio を閉じて再起動し、プロジェクトを再度開いて再試行します。 

      ![GoogleServicesJson ビルド アクション](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>プロジェクトで Notification Hubs を設定する

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging の登録

**AndroidManifest.xml** ファイルを開き、次の `<receiver>` 要素を `<application>` 要素に挿入します。

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Android アプリケーションと通知ハブについて次の情報を収集します。
   
   * **接続文字列のリッスン**: [Azure Portal] のダッシュボードで **[接続文字列の表示]** をクリックします。 この値として接続文字列 *DefaultListenSharedAccessSignature* をコピーします。
   * **ハブ名**: [Azure Portal] からのハブの名前。 たとえば、 *mynotificationhub2*です。
     
2. **ソリューション エクスプローラー** ウィンドウで**プロジェクト**を右クリックし、**[追加]** をポイントして **[クラス]** を選択します。 
4. Xamarin プロジェクトの **Constants.cs** クラスを作成し、このクラスに次の定数値を定義します。 プレースホルダーを実際の値に置き換えます。
    
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
7. **Constants** クラスを作成した場合と同様に、新しいクラス **MyFirebaseIIDService** を作成します。 
8. 次の using ステートメントを **MyFirebaseIIDService.cs** に追加します。
   
    ```csharp
    using Android.App;
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. **MyFirebaseIIDService.cs** で、次の**クラス**宣言を追加し、そのクラスが **FirebaseInstanceIdService** から継承するようにします。
   
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
        using Android.App;
        using Android.Util;
        using Firebase.Messaging;
    ```
13. クラスの宣言の上に次のコードを追加し、クラスが **FirebaseMessagingService** から継承するようにします。
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
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
15. プロジェクトを**構築します**。 
16. デバイスまたは読み込まれたエミュレーターでアプリを**実行する**

## <a name="send-test-notification-from-the-azure-portal"></a>Azure Portal からテスト通知を送信する
アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 これは、デバイスにテスト プッシュ通知を送信します。

![Azure Portal - テスト送信](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、バックエンドに登録されているすべての Android デバイスにブロードキャスト通知を送信しました。 特定の Android デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


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
[Visual Studio と Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
