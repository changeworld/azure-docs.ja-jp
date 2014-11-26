<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、**Azure Notification Hubs** を使用して **Nokia X** の Android アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Nokia Notification Service を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

-   [Nokia Notification service を構成する][Nokia Notification service を構成する]
-   [通知ハブを構成する][通知ハブを構成する]
-   [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
-   [アプリケーションへの通知の送信方法][アプリケーションへの通知の送信方法]
-   [アプリケーションのテスト][アプリケーションのテスト]

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

このチュートリアルには、次のものが必要です。

1.  Nokia X 開発環境 ([ここ][ここ]に示す手順に従って構成できます)。Nokia X 固有のパッケージをインストールし、次の手順に従って Nokia X エミュレーターを設定します。
2.  Nokia X デバイス設定 (オプション) ([ここ][1]に示す手順に従って構成できます)。
3.  Android SDK (Eclipse を使用していることが前提です)。[ここ][2]からダウンロードできます。
4.  Mobile Services Android SDK。[ここ][3]<a>からダウンロードできます。

## <span id="register"></span></a>Nokia Notification service を構成する

1.  [Nokia Notifications API Developer Console (Nokia Notifications API 開発者コンソール)][Nokia Notifications API Developer Console (Nokia Notifications API 開発者コンソール)] にサインインします。

2.  **[Create services (サービスの作成)]** タブに移動し、**[Sender ID (送信者 ID)]** と **[Service description (サービスの説明)]** に入力して新しいサービスを作成します。

    ![][0]

3.  サービスの作成が完了したら、**送信者 ID** と**承認キー**をメモします。

4.  **[My services (マイ サービス)]** タブに移動し、**送信者 ID** と**承認キー**ごとに作成したすべてのサービスを一覧表示できます。

    ![][4]

5.  詳細については、こちらの[リンク][リンク]を参照してください。

## <span id="configure-hub"></span></a>通知ハブを構成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+ 新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][5]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][6]

4.  今作成した通知ハブに移動します。左側の [Service Bus] タブをクリックし、通知ハブを作成した名前空間をクリックしたら、[Notification Hubs] タブをクリックします。

    ![][7]

5.  作成した通知ハブで、上部の **[構成]** タブをクリックします。

    ![][8]

6.  下にスクロールして **nokia x notification settings** を見つけ、Nokia Notification service から取得した承認キーを貼り付けてから、ページ下部の **[保存]** をクリックします。

    ![][9]

7.  ページ上部の **[ダッシュボード]** タブを選択し、下部の **[接続情報]** をクリックします。

    ![][10]

8.  このチュートリアルで後で使用することになる **DefaultListenSharedAccessSignature** および **DefaultFullSharedAccessSignature** に対する 2 つの SAS 接続文字列をメモします。

## <span id="connect-hub"></span></a>通知ハブにアプリケーションを接続する

### 新しい Android プロジェクトを作成する

1.  Eclipse ADT で、新しい Android プロジェクトを作成します ([File] -\> [New] -\> [Android Application])。

2.  Minimum Required SDK が **API 16: Android 4.1 (Jelly Bean)** に設定され、次の 2 つの SDK エントリが使用可能な最新バージョンに設定されていることを確認します。[次へ] を選択し、ウィザードに従い、**[アクティビティの作成]** が選択されていることを確認して、**空のアクティビティ**を作成します。次のボックスで既定のランチャー アイコンを受け入れ、最後のボックスで **[完了]** をクリックします。

    ![][11]

3.  プロジェクトのビルド ターゲットが正しく設定されていることを確認します (このサンプルでは、Platform = 4.1.2、API Level = 16)。プロジェクトを右クリックし、[プロパティ] を選択して、プロジェクト プロパティ ダイアログで Android を選択します。

    ![][12]

4.  プロジェクトに Nokia Notification Service の JAR ファイルを追加します。この Nokia Notifications ヘルパー ライブラリ **push.jar** は、GCM ヘルパー ライブラリと同様の API を提供します。
    Project Properties -\> Java Build Path -\> Libraries -\> Add External JARs と移動して、**\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar** で利用可能な **push.jar** を追加します。ライブラリの Javadoc は、Eclipse IDE がインストールされる **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna** にあります。

5.  push.jar ライブラリも、パッケージ エクスプローラーのプロジェクトの \\libs ディレクトリにコピーしてください。

6.  Notification Hubs Android SDK を[ここ][3]からダウンロードします。.zip ファイルを展開し、Package Explorer で notificationhubs\\notification-hubs-0.3.jar ファイルをプロジェクトの \\libs ディレクトリにコピーします。

    <div class="dev-callout"><b>注</b>
<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
</div>

### コードの追加

1.  **AndroidManifest.xml** ファイルを開き、アプリケーション要素を次のコード行で置き換えます。このとき、Android アプリケーションの作成時に指定したパッケージ名で `[YourPackageName]` を置き換えます。

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  Package Explorer で、(`src` ノードの下の) パッケージを右クリックし、**[新規]**、**[クラス]** の順にクリックして、**ConfigurationSettings.java** という新しいクラスを作成します。
    ![][13]

    サンプルで使用する定数を定義する次のコードを追加します。

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Nokia プッシュ コンソールから構成 **SenderId**、および管理ポータルから **NotificationHubName** と **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) を上記の定数に入力します。

3.  **MainActivity.java** で、次のインポート ステートメントを追加します。

        import com.nokia.push.PushRegistrar;

    次に、onCreate メソッドに次のコードを追加します。

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  **PushIntentService.java** という名前の新しいクラスを追加し、次のコードを追加します。このコードは、通知ハブへの登録を行い、この通知ハブから受信したメッセージも処理します。

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>アプリへの通知の送信方法

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して表示します。

1.  Visual C# の新しいコンソール アプリケーションを作成します。

    ![][14]

2.  [WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK に参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力し、Enter キーを押します。

        Install-Package WindowsAzure.ServiceBus

3.  Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4.  `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>アプリケーションのテスト

実際の電話でこのアプリケーションをテストする場合は、USB ケーブルでコンピューターに電話を接続するだけです。

エミュレーターを使用してこのアプリケーションをテストするには。

1.  上部の Eclipse ツール バーで、[Run] をクリックし、アプリケーションを選択します。

2.  これによって、アプリケーションが接続した電話に読み込まれるか、またはエミュレーターが開始してアプリケーションが読み込まれ、実行されます。

3.  アプリケーションは、Nokia Notification Service から registrationId を取得し、通知ハブに登録します。

    <div class="dev-callout"><b>注</b>
<p>
Android アプリケーションが通知ハブへの登録を完了すると、'Eclipse Logcat' ログに次のようなメッセージが表示されます。
'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  .Net コンソール アプリケーションの使用時にテスト通知を送信するには、Visual Studio で F5 キーを押してアプリケーションを実行すると、通知が送信され、デバイスまたはエミュレーターの上部の通知領域に表示されます。



  [Nokia Notification service を構成する]: #register
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connect-hub
  [アプリケーションへの通知の送信方法]: #send
  [アプリケーションのテスト]: #test-app
  [ここ]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Nokia Notifications API Developer Console (Nokia Notifications API 開発者コンソール)]: https://console.push.nokia.com/ncm/Web/index.jsp
  [0]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [リンク]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [5]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [7]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [12]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [13]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [14]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
