<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

-   [Google Cloud Messaging を有効にする][Google Cloud Messaging を有効にする]
-   [通知ハブを構成する][通知ハブを構成する]
-   [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
-   [アプリへの通知の送信方法][アプリへの通知の送信方法]
-   [アプリケーションのテスト][アプリケーションのテスト]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。

このチュートリアルには、次のものが必要です。

-   Android SDK (前提条件として Eclipse を使用する必要があります)。[ここ][ここ]からダウンロードできます。
-   [Mobile Services Android SDK][Mobile Services Android SDK]

このチュートリアルを完了することは、Android アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <span id="register"></span></a>Google Cloud Messaging を有効にする

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

次に、この API キー値を使用して、通知ハブが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

## <span id="configure-hub"></span></a>通知ハブを構成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][1]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** をクリックします。

    ![][2]

5.  上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

    ![][3]

6.  上部にある **[構成]** タブをクリックし、前のセクションで取得した **API キー**値を入力して、**[保存]** をクリックします。

    ![][4]

7.  上部にある **[ダッシュボード]** タブを選択し、**[接続文字列の表示]** をクリックします。2 つの接続文字列をメモします。

これで、通知ハブが GCM と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <span id="connecting-app"></span></a>通知ハブにアプリケーションを接続する

### 新しい Android プロジェクトの作成

1.  Eclipse ADT で新しい Android プロジェクトを作成します ([File]、[New]、[Android Application])。

    ![][5]

2.  **[必要最小限の SDK]** が *[API 8: Android 2.2 (Froyo)]* に設定され、次の 2 つの SDK エントリが使用可能な最新バージョンに設定されていることを確認します。[次へ] を選択し、ウィザードに従い、**[Create activity]** が選択されていることを確認して、空のアクティビティを作成します。次のボックスで既定のランチャー アイコンを受け入れ、最後のボックスで **[完了]** をクリックします。

    ![][6]

### プロジェクトへの Google Play Services の追加

[WACOM.INCLUDE [Add Play Services][Add Play Services]]

### コードの追加

1.  Notification Hubs Android SDK を[ここ][Mobile Services Android SDK]からダウンロードします。.zip ファイルを展開し、Package Explorer で notificationhubs\\notification-hubs-0.1.jar ファイルをプロジェクトの \\libs ディレクトリにコピーします。

2.  [Mobile Services Android SDK][Mobile Services Android SDK] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-n.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

    <div class="dev-callout"><b>注</b>
<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
</div>

    ここで、GCM から *registrationId* を取得し、registrationId を使用してアプリケーション インスタンスを通知ハブに登録するようにアプリケーションを設定します。

3.  AndroidManifest.xml ファイルで、<uses-sdk></uses-sdk>要素のすぐ下に次の行を追加します。`<your package>` を、手順 1 でアプリケーションに選択したパッケージに置き換えます (この例では `com.yourCompany.wams_notificationhubs`)。

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  **MainActivity** クラスで、次のステートメントを追加します。

        import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  クラスの最上位に、次のプライベート メンバーを追加します。

    <div class="dev-callout"><b>注</b>
<p>SENDER_ID に、先に取得したプロジェクト番号が設定されていることを確認します。</p>
</div>

        private String SENDER_ID = "<your project number>";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;

6.  **OnCreate** メソッドで次のコードを追加し、プレースホルダーを、前のセクションで取得したリッスン アクセス権を持つ接続文字列と、(完全な URL **ではなく**) Azure でハブのページの最上部に表示される通知ハブの名前に置き換えます。

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

        gcm = GoogleCloudMessaging.getInstance(this);

        String connectionString = "<your listen access connection string>";
        hub = new NotificationHub("<your notification hub name>", connectionString, this);

        registerWithNotificationHubs();

7.  MainActivity.java で、次のメソッドを作成します。

        @SuppressWarnings("unchecked")
        private void registerWithNotificationHubs() {
           new AsyncTask() {
              @Override
              protected Object doInBackground(Object... params) {
                 try {
                    String regid = gcm.register(SENDER_ID);
                    hub.register(regid);
                 } catch (Exception e) {
                    return e;
                 }
                 return null;
             }
           }.execute(null, null, null);
        }

8.  Android には通知が表示されないため、独自の受信者を記述する必要があります。**AndroidManifest.xml** で、`<application/>` 要素内に次の要素を追加します。

    <div class="dev-callout"><b>注</b>
<p>プレースホルダーは、パッケージ名に置き換えてください。</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  Package Explorer で (`src` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

10. **[Name]** に「`MyHandler`」と入力し、**[Superclass]** に「`com.microsoft.windowsazure.notifications.NotificationsHandler`」と入力します。**[Finish]** をクリックします。

    ![][7]

    これで、新しい MyHandler クラスが作成されます。

11. 次の import ステートメントを追加します。

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. 次のコードをクラスに追加します。

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;


        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

## <a name="send"></a>アプリへの通知の送信方法

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**モバイル サービスでのプッシュ通知の使用**」 ([.NET バックエンド][.NET バックエンド] | [JavaScript バックエンド][.NET バックエンド]) を参照してください。REST API を使用して通知を送信する方法の例については、「**Java/PHP から Notification Hubs を使用する方法**」 ([Java][Java] | [PHP][PHP]) を参照してください。

1.  Visual Studio で、**[ファイル]** メニューから、**[新規作成]**、**[プロジェクト]** をクリックし、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]**、**[OK]** の順にクリックします。

    ![][8]

    これで、新しいコンソール アプリケーション プロジェクトが作成されます。

2.  **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

    これで、パッケージ マネージャー コンソールが表示されます。

3.  コンソール ウィンドウで、次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus

    これで、[WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK への参照が追加されます。

4.  Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5.  **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    "hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。

    > [WACOM.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

6.  次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>アプリケーションのテスト

エミュレーターでアプリケーションをテストする前に、こうしたエミュレーターの構成手順を完了する必要があります (物理デバイスでテストする場合はスキップ)。

1.  Google API をサポートしている Android Virtual Device (AVD) を使用します。

2.  **[ウィンドウ]** で **[Android 仮想デバイス マネージャー]** をクリックし、デバイスを選択してから **[編集]** をクリックします。

    ![][9]

3.  **[Target]** で **[Google APIs]** を選択し、**[OK]** をクリックします。

    ![][10]

4.  プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、**[Settings]** に移動して **[Add Account]** をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。

次の手順を使用して、アプリケーションをデバイスまたはエミュレーターで実行します。

1.  上部の Eclipse ツール バーで、**[実行]** をクリックし、アプリケーションを選択します。

    これによりエミュレーターが起動し (エミュレーターを使用している場合)、アプリケーションが読み込まれ、実行されます。アプリにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

2.  Visual Studio で F5 キーを押して、コンソール アプリケーションを実行します。

    通知がアプリケーションに送信されます。

3.  アイコンが通知領域 (左上隅) に表示されたら、通知ドロワを引き出して、通知を表示します。

    ![][11]

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要][Windows Azure 通知ハブの概要]」を参照してください。

<!-- Images. --> 
<!-- URLs. -->

  [Windows ユニバーサル]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows ユニバーサル"
  [Windows Phone]: /ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ja-jp/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ja-jp/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Google Cloud Messaging を有効にする]: #register
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [アプリへの通知の送信方法]: #send
  [アプリケーションのテスト]: #run-app
  [ここ]: http://go.microsoft.com/fwlink/?LinkId=389797
  [Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /ja-jp/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ja-jp/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
