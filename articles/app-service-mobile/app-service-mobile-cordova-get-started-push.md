<properties
	pageTitle="Azure Mobile Apps を使用した Apache Cordova アプリへのプッシュ通知の追加 |Azure App Service"
	description="Azure Mobile Apps を使用して Apache Cordova アプリにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Apache Cordova アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 概要

このチュートリアルでは、[Apache Cordova クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。最初に、このチュートリアルの基になっている [Apache Cordova クイック スタート] チュートリアルを完了しておく必要があります。ASP.NET バックエンドがあり、ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)]」を参照してください。

##<a name="prerequisites"></a>前提条件

このチュートリアルでは、Visual Studio 2015 内で開発し、Google Android エミュレーターで実行される Apache Cordova アプリケーションについて説明します。また、他のエミュレーターやデバイスのほか、別の開発プラットフォームでこのチュートリアルを実行することもできます。

このチュートリアルを完了するには、以下が必要です。

* 確認済みの電子メール アドレスがある [Google アカウント]
* [Visual Studio Community 2015] 以降のバージョンがインストールされた PC
* [Apache Cordova の Visual Studio ツール]
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* 完成した [Apache Cordova クイック スタート] プロジェクト。他のチュートリアル ([認証]など) を先に完了することもできますが、必須ではありません
* Android デバイス

プッシュ通知は Android エミュレーターでサポートされていますが、動作が安定していないため、エミュレーターでプッシュ通知をテストすることはお勧めしません。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Google Cloud Messaging を有効にする

Google Android プラットフォームをターゲットとしているため、Google Cloud Messaging を有効にする必要があります。Apple iOS デバイスをターゲットとする場合は、APNs サポートを有効にします。また、Microsoft Windows デバイスをターゲットとする場合は、WNS または MPNS のサポートを有効にします。

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>プッシュ要求を送信するようにモバイル アプリ バックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>サーバー プロジェクトをプッシュ通知を送信するように更新する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>USB デバッグ用に Android デバイスを構成する

アプリケーションを Android デバイスにデプロイする前に、USB デバッグを有効にする必要があります。Android フォンで次の手順を実行します。

1. **[設定]**、**[端末情報]** の順に移動します。
2. デベロッパー モードが有効になるまで **[ビルド番号]** をタップします。
3. **[設定]** に戻ります。
4. **[開発者向けオプション]** を選択します。
5. **[USB デバッグ]** をオンにします。
6. Android フォンを USB ケーブルで開発用 PC に接続します。

このチュートリアルのテストでは、Android 6.0 (Marshmallow) リリースを実行している Google Nexus 5X を使用しました。ただし、その手法は最新のどの Android リリースでも共通です。

##<a name="add-push-to-app"></a>アプリケーションにプッシュ通知を追加する

Apache Cordova アプリ プロジェクトでプッシュ通知を処理する準備が整っていることを確認する必要があります。

### Apache Cordova プッシュ プラグインをインストールする

Apache Cordova アプリケーションでは、デバイスやネットワークの機能をネイティブで処理しません。これらの機能は、[npm] または GitHub で公開されているプラグインによって提供されます。ネットワーク プッシュ通知の処理には、`phonegap-plugin-push` プラグインを使用します。コマンド ラインからインストールするには、次のコマンドを実行します。

    cordova plugin add phonegap-plugin-push

Visual Studio 内にプラグインをインストールするには:

1.  ソリューション エクスプローラー内から `config.xml` ファイルを開きます。
2.  **[プラグイン]** (左側)、**[カスタム]** (上側) の順にクリックします。
3.  インストール元として **[Git]** を選択し、「`https://github.com/phonegap/phonegap-plugin-push`」と入力します。
4.  インストール元の横にある矢印をクリックし、**[追加]** をクリックします。

これでプッシュ プラグインがインストールされます。

### Android Google Play Services をインストールする

PhoneGap プッシュ プラグインでは、プッシュ通知に Google Play Services を利用します。インストールするには、次のようにします。

1.  **Visual Studio** を開きます。
2.  **[ツール]**、**[Android]**、**[Android SDK Manager]** の順にクリックします。
3.  Extras フォルダー内の、インストールされていない必要な各 SDK の横にあるチェック ボックスをオンにします。次のパッケージが必要です。
    * Android Support Library バージョン 23 以上
    * Android Support Repository バージョン 20 以上
    * Google Play Services バージョン 27 以上
    * Google Repository バージョン 22 以上
4.  **[パッケージのインストール]** をクリックします。
5.  インストールが完了するのを待ちます。

最新の必要なライブラリは、「[phonegap-plugin-push installation documentation (phonegap-plugin-push インストール ドキュメント)]」に記載されています。

### スタートアップ時のプッシュにデバイスを登録する

ログイン プロセスのコールバック時、つまり `onDeviceReady()` メソッドの一番下に `registerForPushNotifications()` の呼び出しを追加します。

    // Login to the service
    client.login('google')
        .then(function () {
            // Create a table reference
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

            registerForPushNotifications();

        }, handleError);

次のように `registerForPushNotifications()` を実装します。

    /**
     * Register for Push Notifications - requires the phonegap-plugin-push be installed
     */
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: {
                senderID: 'YourProjectID'
            },
            ios: {
                alert: 'true',
                badge: 'true',
                sound: 'true'
            },
            wns: {

            }
        });

        pushRegistration.on('registration', function (data) {
            client.push.register('gcm', data.registrationId);
        });

        pushRegistration.on('notification', function (data, d2) {
            alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
    }

_YourProjectID_ を、[Google デベロッパー コンソール]から入手したアプリのプロジェクト ID (数値) に置き換えます。

## 発行されたモバイル サービスに対してアプリケーションをテストする

Android フォンを USB ケーブルで直接接続して、アプリケーションをテストできます。**Google Android エミュレーター**の代わりに、**[デバイス]** を選択します。Visual Studio によってデバイスにアプリケーションがダウンロードされ、アプリケーションが実行されます。デバイスでアプリケーションを操作します。

開発環境を強化しましょう。[Mobizen] などの画面共有アプリケーションは、PC の Web ブラウザーに Android の画面を表示できるため、Android アプリケーションの開発に役立ちます。

##<a name="next-steps"></a>次のステップ

* プッシュ通知の詳細については、「[Azure Notification Hubs]」を参照してください。
* まだ認証を追加していない場合は、チュートリアルの続きとして、Apache Cordova アプリに[認証を追加]してください。

<!-- URLs -->
[認証を追加]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova クイック スタート]: app-service-mobile-cordova-get-started.md
[認証]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google アカウント]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google デベロッパー コンソール]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push installation documentation (phonegap-plugin-push インストール ドキュメント)]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/ja-JP/features/cordova-vs.aspx
[Azure Notification Hubs]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0218_2016-->