---
title: Azure Notification Hubs を使用して Kindle アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Kindle アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926707"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Notification Hubs の使用 (Kindle アプリ)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルでは、Azure Notification Hubs を使用して Kindle アプリケーションにプッシュ通知を送信する方法について説明します。 Amazon Device Messaging (ADM) を使用してプッシュ通知を受信する空の Kindle アプリを作成します。

このチュートリアルでは、次のタスクを実行するコードを作成および更新します。

> [!div class="checklist"]
> * 開発者ポータルへの新しいアプリの追加
> * API キーの作成
> * 通知ハブの作成と構成
> * アプリケーションの設定
> * ADM メッセージ ハンドラーの作成
> * アプリへの API キーの追加
> * アプリの実行
> * テスト通知を送信する

## <a name="prerequisites"></a>前提条件

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE)。
- 「[開発環境を設定する](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html)」の手順に従い、Kindle の開発環境を設定します。

## <a name="add-a-new-app-to-the-developer-portal"></a>開発者ポータルへの新しいアプリの追加

1. [Amazon 開発者ポータル](https://developer.amazon.com/apps-and-games/console/apps/list.html)にサインインします。
2. **[Add New App]\(新しいアプリの追加\)** を選択し、 **[Android]** を選択します。  

    ![[Add New App]\(新しいアプリの追加\) ボタン](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. **[New App Submission]\(新しいアプリの送信\)** ページで、次の手順に従って**アプリケーション キー**を取得します。
    1. **[App title]\(アプリ タイトル\)** に名前を入力します。
    2. **カテゴリ**を選択します (例: [education]\(教育\))
    4. **[Customer support email address]\(カスタマー サポート メール アドレス\)** フィールドにメール アドレスを入力します。 
    5. **[保存]** を選択します。

        ![[New App Submission]\(新しいアプリの送信\) ページ](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  上部の **[Mobile Ads]\(モバイル広告\)** タブに切り替えて、次の手順を実行します。 
    1. アプリが主に 13 歳未満の子供向けかどうかを指定します。 このチュートリアルでは、 **[No]\(いいえ\)** を選択します。
    2. **[Submit]\(送信\)** をクリックします。 

        ![[Mobile Ads]\(モバイル広告\) ページ](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. **[Mobile Ads]\(モバイル広告\)** ページの**アプリケーション キー**をコピーします。 

        ![アプリケーション キー](./media/notification-hubs-kindle-get-started/application-key.png)
3.  上部にある **[Apps & Services]\(アプリとサービス\)** メニューを選択し、一覧からアプリケーションを選択します。 

    ![一覧からのアプリの選択](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. **[Device Messaging]** タブに切り替えて、次の手順に従います。 
    1. **[Create a New Security Profile]\(新しいセキュリティ プロファイルの作成\)** を選択します。
    2. セキュリティ プロファイルの**名前**を入力します。 
    3. セキュリティ プロファイルの**説明**を入力します。 
    4. **[保存]** を選択します。 
    5. 結果ページから **[View Security Profile]\(セキュリティ プロファイルの表示\)** を選択します。 
5. ここで、 **[Security Profile]\(セキュリティ プロファイル\)** ページで、次の手順を実行します。 
    1. **[Web Settings]\(Web 設定\)** タブに切り替えて、 **[Client ID]\(クライアント ID\)** と **[Client Secret]\(クライアント シークレット\)** の値を後で使用するためにコピーしておきます。 

        ![クライアント ID とシークレットの取得](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. **[Android/Kindle Settings]\(Android/kindle 設定\)** ページに切り替えて、ページを開いたままにしておきます。 これらの値は次のセクションで入力します。 

## <a name="create-an-api-key"></a>API キーの作成
1. 管理者特権でコマンド プロンプトを開きます。
2. Android SDK フォルダーに移動します。
3. 次のコマンドを入力します。

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. **keystore** パスワードに「**android**」と入力します。
5. **MD5** と **SHA256** のフィンガープリントをコピーします。 
6. 開発者ポータルに戻り、 **[Android/Kindle Settings]\(Android/kindle 設定\)** タブで、次の手順を実行します。 
    1. **API キーの名前**を入力します。 
    2. アプリの**パッケージの名前** (例: **com.fabrikam.mykindleapp**) および **MD5** の値を入力します。
        
        >[!IMPORTANT]
        > Android Studio でアプリを作成する場合は、ここで指定したものと同じパッケージ名を使用します。 
    1. 先ほどコピーした **MD5 署名**を貼り付けます。 
    2. 先ほどコピーした **SHA256 署名**を貼り付けます。  
    3. **[Generate New Key]\(新しいキーの生成\)** を選択します。

        ![Android/Kindle 設定 - キーの生成](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. ここで、一覧から **[Show]\(表示\)** を選択して API キーを表示します。 

        ![Android/Kindle 設定 - API キーの表示](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. **[API Key Details]\(API キーの詳細\)** ウィンドウで、API キーをコピーして別の場所に保存します。 次に、右隅にある **[X]** を選択して、ウィンドウを閉じます。 


## <a name="create-and-configure-a-notification-hub"></a>通知ハブの作成と構成

1. 「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」の記事の手順に従い、通知ハブを作成します。 
2. **[Settings]\(設定\)** メニューの下にある **[Amazon (ADM)]** を選択します。
3. 次に、先ほど保存した**クライアント ID** と**クライアント シークレット**を貼り付けます。 
4. ツールバーの **[保存]** を選択します。 

    ![通知ハブの ADM 設定の構成](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. 左側のメニューの **[Access Policies]\(アクセス ポリシー\)** を選択し、**DefaultListenSharedAccessSignature** ポリシーに対する接続文字列の**コピー** ボタンを選択します。 それを別の場所に保存します。 この記事の後半でソース コード内で使用します。 

    ![通知ハブ - リッスン接続文字列](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>アプリケーションの設定

1. Android Studio を起動します。 
2. **[File]\(ファイル\)** を選択し、 **[New]\(新規\)** をポイントして、 **[New Project]\(新しいプロジェクト\)** を選択します。 
3. **[Choose your project]\(プロジェクトの選択\)** ウィンドウの **[Phone and Tablet]\(電話およびタブレット\)** タブで、 **[Empty Activity]\(空のアクティビティ\)** 、 **[次へ]** の順に選択します。 
4. **[Configure your project]\(プロジェクトの構成\)** ウィンドウで、次の手順を実行します。
    1. **アプリケーションの名前**を入力します。 Amazon 開発者ポータルで作成したアプリケーションの名前と一致させることもできます。 
    2. **パッケージの名前**を入力します。 
        
        >[!IMPORTANT]
        >パッケージ名は、Amazon 開発者ポータルで指定したパッケージ名と一致させる必要があります。
    3. 残りの値を確認し、必要に応じて更新します。 
    4. **[完了]** を選択します。 

        ![Android プロジェクトの構成](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. [Android 用 Amazon Developer SDK](https://developer.amazon.com/sdk-download) ライブラリをハード ディスクにダウンロードします。 SDK zip ファイルを解凍します。
6. Android Studio で、フォルダ構造を **[Android]** から **[Project]\(プロジェクト\)** に変更します (まだ **[Project]\(プロジェクト\)** に設定されていない場合)。 

    ![Android Studio - プロジェクト構造への切り替え](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. **app** を展開し、ツリー ビュー内の **libs** フォルダーを参照します。     
8. エクスプ ローラー ウィンドウで、Amazon Android SDK をダウンロードしたフォルダーに移動します。
9. **CTRL** キーを押して、**amazon-device-messaging-1.0.1.jar** ファイルをツリー ビューの **lib** ノードにドロップします。 

    ![Android Studio - Amazon Device Messaging JAR の追加](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. **[Copy]\(コピー\)** ウィンドウで、 **[OK]** を選択します。 **[Copy]\(コピー\)** ウィンドウの代わりに **[Move]\(移動\)** ウィンドウが表示された場合、ウィンドウを閉じ、**CTRL** キーを押したままドラッグ アンド ドロップ操作をやり直してください。 

    ![Android Studio - JAR のコピー](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. **app の build.gradle** ファイルの **dependencies** セクションに次のステートメントを追加します。`implementation files('libs/amazon-device-messaging-1.0.1.jar')` 

    ![Android Studio - app の build.gradle への ADM の追加](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. **app** の `Build.Gradle` ファイルで、**dependencies** セクションに次の行を追加します。 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. **dependencies** セクションの**後に**次のリポジトリを追加します。

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. **app** の **build.gradle** ファイルのエディターで、ツール バーの **[Sync now]\(今すぐ同期\)** を選択します。 

    ![Android Studio - app の build.gradle の同期](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. ツリー ビューの Android 構造に切り替えて戻します。  ルート マニフェスト要素に Amazon 名前空間を追加します。

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![マニフェスト内の Amazon 名前空間](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. マニフェスト要素の下の最初の要素としてアクセス許可を追加します。 **[YOUR PACKAGE NAME]** を、アプリケーションを作成するときに使用したパッケージに置き換えます。

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. 次の要素をアプリケーション要素の最初の子として挿入します。 **[YOUR PACKAGE NAME]** を、アプリケーションを作成するときに使用したパッケージ名に置き換えます。 MyADMMessageHandler クラスは次の手順で作成します。 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>ADM メッセージ ハンドラーの作成

1. 次の図に示すように、`com.amazon.device.messaging.ADMMessageHandlerBase` から継承するプロジェクト内の `com.fabrikam.mykindleapp` パッケージに新しいクラスを追加し、`MyADMMessageHandler` という名前を付けます。

    ![MyADMMessageHandler クラスの作成](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. 次の `import` ステートメントを `MyADMMessageHandler` クラスに追加します。

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. 作成したクラスに次のコードを追加します。 `[HUB NAME]` と `[LISTEN CONNECTION STRING]` は通知ハブとリッスン接続文字列の名前に置き換えてください。 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>アプリへの API キーの追加
1. 次の手順に従い、assets フォルダーをプロジェクトに追加します。 
    1. **[Project]\(プロジェクト\)** ビューに切り替えます。 
    2. **[app]** を右クリックします。
    3. **[新規]** を選択します。
    4. **[Folder]\(フォルダー\)** を選択します。 
    5. 次に、 **[Assets Folder]\(Assets フォルダー\)** を選択します。 

        ![assets フォルダーの追加メニュー](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. **[Configure Component]\(コンポーネントの構成\)** ページで、次の手順を実行します。
        1. **[Change folder location]\(フォルダーの場所の変更\)** を選択します。
        2. フォルダーが `src/main/assets` に設定されていることを確認します。
        3. **[完了]** を選択します。 
        
            ![assets フォルダーの構成](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. **api_key.txt** という名前のファイルを **assets** フォルダーに追加します。 ツリー ビューで、 **[app]** 、 **[src]** 、 **[main]** の順に展開し、 **[assets]** を右クリックし、 **[New]\(新規\)** をポイントして、 **[File]\(ファイル\)** を選択します。 ファイル名に「**api_key.txt**」と入力します。 手順 3. 
5. Amazon 開発者ポータルで生成した API キーを api_key.txt にコピーします。 
6. プロジェクトをビルドします。 

## <a name="run-the-app"></a>アプリの実行
1. Kindle デバイスで、上部からスワイプして **[設定]** 、 **[マイ アカウント]** の順にクリックし、有効な Amazon アカウントで登録します。
2. Kindle デバイスで、Android Studio からアプリを実行します。 

> [!NOTE]
> 問題が発生した場合は、エミュレーター (またはデバイス) の時間を確認します。 時刻値は正確である必要があります。 Kindle エミュレーターの時刻を変更するには、Android SDK platform-tools ディレクトリで次のコマンドを実行します。

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>通知メッセージを送信する

.NET を使用してメッセージを送信するには:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

サンプル コードについては、[GitHub の例](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs)を参照してください。

![][7]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、バックエンドに登録されているすべての Kindle デバイスにブロードキャスト通知を送信しました。 特定の Kindle デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。次のチュートリアルは、特定の Android デバイスにプッシュ通知を送信する方法を示していますが、特定の Kindle デバイスにプッシュ通知を送信する場合も同じロジックを使用できます。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
