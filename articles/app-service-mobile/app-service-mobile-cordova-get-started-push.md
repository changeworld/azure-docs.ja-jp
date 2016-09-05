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
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Apache Cordova アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Overview

このチュートリアルでは、[Apache Cordova クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。最初に、このチュートリアルの基になっている [Apache Cordova クイック スタート] チュートリアルを完了しておく必要があります。ASP.NET バックエンドがあり、ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)]」を参照してください。

##<a name="prerequisites"></a>前提条件

このチュートリアルでは、Visual Studio 2015 内で開発し、Google Android エミュレーター、Android デバイス、Windows デバイス、iOS デバイスで実行される Apache Cordova アプリケーションについて説明します。

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio Community 2015] 以降のバージョンがインストールされた PC
* [Apache Cordova の Visual Studio ツール]
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* 完成した [Apache Cordova クイック スタート] プロジェクト他のチュートリアル ([認証]など) を先に完了することもできますが、必須ではありません
* (Android) 検証済みの電子メール アドレスがある [Google アカウント]と Android デバイス
* (iOS) Apple Developer Program メンバーシップと iOS デバイス (iOS シミュレーターはプッシュ通知をサポートしていません)
* (Windows) Windows ストア開発者アカウントと Windows 10 デバイス

プッシュ通知は Android エミュレーターでサポートされていますが、動作が安定していないため、エミュレーターでプッシュ通知をテストすることはお勧めしません。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##プッシュ通知を送信するようにサーバー プロジェクトを更新する

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Cordova アプリを変更してプッシュ通知を受信する

Apache Cordova アプリ プロジェクトでプッシュ通知を処理する準備が整っていることを確認する必要があります。Cordova のプッシュ プラグインと、プラットフォーム固有のプッシュ サービスをインストールする必要があります。

#### プロジェクトで Cordova のバージョンを更新する

プロジェクトが以前のバージョンの Cordova で構成されている場合、クライアント プロジェクトを Cordova 6.1.1 に更新することをお勧めします。プロジェクトを更新するには、config.xml を右クリックして構成デザイナーを開きます。[プラットフォーム] タブをクリックし、**[Cordova CLI]** ボックスで 6.1.1 を選択します。

**[ビルド]**、**[ソリューションのビルド]** の順に選択してプロジェクトを更新します。

#### プッシュ プラグインをインストールする

Apache Cordova アプリケーションでは、デバイスやネットワークの機能をネイティブで処理しません。これらの機能は、[npm](https://www.npmjs.com/) または GitHub で公開されているプラグインによって提供されます。ネットワーク プッシュ通知の処理には、`phonegap-plugin-push` プラグインを使用します。

次のいずれかの方法でプッシュ プラグインをインストールできます。

**コマンド プロンプトから:**

次のコマンドを実行します。

    cordova plugin add phonegap-plugin-push

**Visual Studio 内から:**

1.  ソリューション エクスプローラーで `config.xml` ファイルを開き、**[プラグイン]**、**[カスタム]** の順にクリックします。次に、**[Git]** をインストール元として選択し、「`https://github.com/phonegap/phonegap-plugin-push`」をソースとして入力します。

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  インストール元の横にある矢印をクリックします。

3. Google デベロッパー コンソール プロジェクト用のプロジェクト ID (数値) を既に所有している場合は、**SENDER\_ID** のボックスに追加できます。それ以外の場合は、777777 などのプレース ホルダーの値を入力します。Android を対象としている場合は、後でこの値を config.xml で更新することができます。

4. **[追加]** をクリックします。

これでプッシュ プラグインがインストールされました。

####デバイス プラグインをインストールする

プッシュ プラグインのインストールと同じ手順に従います。ただし、デバイス プラグインはコア プラグインの一覧の中に表示されます (**[プラグイン]**、**[コア]** の順にクリックして表示します)。このプラグインはプラットフォーム名 (`device.platform`) の取得に必要です。

#### スタートアップ時のプッシュにデバイスを登録する

最初に、最小限の Android コードを追加します。後で、iOS または Windows 10 での実行に必要な少々の変更を行います。

1. ログイン プロセスのコールバック時、つまり **onDeviceReady** メソッドの一番下に **registerForPushNotifications** の呼び出しを追加します。

		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				    // Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	この例では、認証が成功した後の **registerForPushNotifications** の呼び出しを表しています。これはアプリでプッシュ通知と認証の両方を使用する場合にお勧めします。

2. 新しい **registerForPushNotifications** メソッドを次のように追加します。

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) 上記のコードで、`Your_Project_ID` を、[Google デベロッパー コンソール]から入手したアプリのプロジェクト ID (数値) に置き換えます。

## (省略可能) Android でアプリを構成して実行する

このセクションを完了すると、Android 用のプッシュ通知を有効にすることができます。

####<a name="enable-gcm"></a>Google Cloud Messaging を有効にする

初期状態で Google Android プラットフォームをターゲットとしているため、Google Cloud Messaging を有効にする必要があります。また、Microsoft Windows デバイスをターゲットとする場合は、WNS のサポートを有効にします。

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>GCM を使用してプッシュ要求を送信するようにモバイル アプリ バックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####Android 用に Cordova アプリを構成する

Cordova アプリで config.xml を開き、`Your_Project_ID` を [Google デベロッパー コンソール]から入手したアプリのプロジェクト ID (数値) に置き換えます。

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

index.js を開き、コードを更新して、プロジェクト ID (数値) を使用できるようにします。

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>USB デバッグ用に Android デバイスを構成する

アプリケーションを Android デバイスにデプロイする前に、USB デバッグを有効にする必要があります。Android フォンで次の手順を実行します。

1. **[設定]**、**[端末情報]** の順に移動して、デベロッパー モードが有効になるまで (約 7 回) **[ビルド番号]** をタップします。

2. **[設定]**、**[開発者向けオプション]** の順に戻り、**[USB デバッグ]** を有効にしてから、Android フォンを USB ケーブルで開発用 PC に接続します。

このチュートリアルのテストでは、Android 6.0 (Marshmallow) を実行している Google Nexus 5X を使用しました。ただし、その手法は最新のどの Android リリースでも共通です。

#### Google Play Services をインストールする

プッシュ プラグインでは、プッシュ通知に Android Google Play Services を利用します。

1.  **Visual Studio** で **[ツール]**、**[Android]**、**[Android SDK Manager (Android SDK マネージャー)]** の順にクリックし、**Extras** フォルダーを展開して、次の SDK がインストールされていれば、それらのチェック ボックスをオンにして確定します。
    * Android Support Repository バージョン 20 以上
    * Google Play Services バージョン 27 以上
    * Google Repository バージョン 22 以上

2.  **[Install Packages (パッケージのインストール)]** をクリックして、インストールが完了するのを待ちます。

最新の必要なライブラリは、[phonegap-plugin-push のインストール ドキュメント]に記載されています。

#### Android 上のアプリでプッシュ通知をテストする

ここで、アプリを実行して TodoItem テーブルにアイテムを挿入することにより、プッシュ通知をテストできます。同じバックエンドを使用している限り、同じデバイスまたは 2 番目のデバイスからテストを実行できます。次のいずれかの方法により、Android プラットフォームで Cordova アプリをテストします。

- **物理デバイス上:** Android デバイスを USB ケーブルで開発用コンピューターに接続します。**Google Android エミュレーター**の代わりに、**[デバイス]** を選択します。Visual Studio によってデバイスにアプリケーションがデプロイされ実行されます。デバイスでアプリケーションを操作できるようになります。開発環境を強化しましょう。[Mobizen] などの画面共有アプリケーションは、PC の Web ブラウザーに Android の画面を表示できるため、Android アプリケーションの開発に役立ちます。

- **Android エミュレーター上:** エミュレーターで実行するときに必要な追加の構成手順があります。

	次に示すように Android Virtual Device (AVD) Manager で Google API がターゲットとして設定された仮想デバイスに対してデプロイまたはデバッグする必要があります。

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	より高速な x86 エミュレーターを使用する場合は、[HAXM ドライバーをインストール](https://taco.visualstudio.com/ja-JP/docs/run-app-apache/#HAXM)した後、これを使用できるようにエミュレーターを構成します。

	**[Apps (アプリケーション)]**、**[Settings (設定)]**、**[Add account (アカウントの追加)]** の順にクリックして、Google アカウントを Android デバイスに追加し、プロンプトに従って既存の Google アカウントをデバイスに追加します (新規のアカウントを作成せずに、既存のアカウントを使用することをお勧めします)。

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	これまでと同様に ToDoList アプリを実行し、新しい ToDo 項目を挿入します。今回は、通知領域に通知アイコンが表示されます。通知ドロアーを開いて、通知の全文を表示できます。

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(省略可能) iOS で構成して実行する

このセクションでは、iOS デバイス上で Cordova プロジェクトを実行します。iOS デバイスを使用していない場合は、このセクションを省略できます。

####iOS remotebuild エージェントを Mac またはクラウド サービスにインストールして実行する

Visual Studio を使用して iOS で Cordova アプリを実行する前に、[iOS セットアップ ガイド](http://taco.visualstudio.com/ja-JP/docs/ios-guide/)の手順に従って remotebuild エージェントのインストールと実行を行います。

iOS 用アプリのビルドが可能であることを確認します。Visual Studio で iOS 用にビルドするには、セットアップ ガイドの手順の実施が必要です。Mac を所持していない場合は、MacInCloud などのサービスで remotebuild エージェントを使用すれば、iOS 用にビルドが可能です。詳細については、[クラウドでの iOS アプリの実行](http://taco.visualstudio.com/ja-JP/docs/build_ios_cloud/)に関するページを参照してください。

>[AZURE.NOTE] iOS でプッシュ プラグインを使用するには XCode 7 以上が必要です。

####アプリ ID として使用する ID を探す

アプリをプッシュ通知に登録する前に、Cordova アプリで config.xml を開き、ウィジェット要素内の `id` 属性の値を特定して、後で使用できるようにコピーしておきます。次の XML では、ID は `io.cordova.myapp7777777` です。

		<widget defaultlocale="ja-JP" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

後で Apple の開発者ポータルでアプリ ID を作成するときに、この識別子を使用します (開発者ポータルで別のアプリ ID を作成して使用する場合は、このチュートリアルの後の方で示すいくつかの追加手順を実施して、この ID を config.xml 内で変更する必要があります。ウィジェット要素の ID は、開発者ポータルのアプリ ID と一致している必要があります)。

####Apple の開発者ポータルにプッシュ通知のアプリを登録する

[AZURE.INCLUDE [Notification Hubs Xamarin で Apple プッシュ通知を有効にする](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####プッシュ通知を送信するように Azure を構成する

1. [Azure ポータル](https://portal.azure.com/)にログインします。**[参照]**、**[Mobile Apps]**、作成したモバイル アプリ、**[設定]**、**[プッシュ]**、**[Apple (APNS)]**、**[証明書のアップロード]** の順にクリックします。以前にエクスポートした .p12 プッシュ証明書ファイルをアップロードします。開発とテスト用に開発プッシュ証明書を作成した場合は、**[サンドボックス]** を選択します。それ以外の場合は、**[運用]** を選択します。これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####アプリ ID がCordova アプリと一致することを確認する

Apple の開発者アカウントで作成したアプリ ID が既に config.xml のウィジェット要素の ID と一致している場合、この手順は省略可能です。ただし、ID が一致しない場合は、以下の手順を実行します。

1. platforms フォルダーをプロジェクトから削除します。

2. plugins フォルダーをプロジェクトから削除します。

3. node\_modules フォルダーをプロジェクトから削除します。

4. config.xml のウィジェット要素の ID 属性を更新して、Apple の開発者アカウントで作成したアプリ ID を使用できるようにします。

5. プロジェクトをリビルドします。

#####iOS アプリでプッシュ通知をテストする

1. Visual Studio で、**iOS** がデプロイ ターゲットとして選択されていることを確認してから、**[デバイス]** を選択して、接続した iOS デバイス上で実行します。

	iTunes を使用中の PC に接続した iOS デバイス上で実行することができます。iOS シミュレーターでは、プッシュ通知はサポートされていません。

2. Visual Studio で **[実行]** または **F5** キーを押してプロジェクトをビルドし、iOS デバイスでアプリを起動します。次に、**[OK]** をクリックして、プッシュ通知を受け入れます。

	>[AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

3. アプリケーションで、タスクを入力し、プラス (+) アイコンをクリックします。

4. 通知が受信されたことを確認し、[OK] をクリックして通知を破棄します。

##(省略可能) Windows で構成して実行する

このセクションでは、Windows 10 デバイス上で Apache Cordova アプリ プロジェクトを実行します (Windows 10 では PhoneGap プッシュ プラグインがサポートされています)。Windows デバイスを使用していない場合は、このセクションを省略できます。

####WNS を使用して Windows アプリをプッシュ通知に登録する

Visual Studio でストアのオプションを使用するには、ソリューション プラットフォームの一覧から **Windows x64** や **Windows x86** などの Windows ターゲットを選択します (プッシュ通知を行う場合、**Windows-AnyCPU** は選択しないでください)。

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####WNS 用に通知ハブを構成する

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####Cordova アプリを構成して Windows プッシュ通知をサポートする

構成デザイナーを開き (config.xml を右クリックして **[ビュー デザイナー]** を選択)、**[Windows]** タブを選択し、**[Windows 対象バージョン]** で **[Windows 10]** を選択します。

>[AZURE.NOTE] Cordova 5.1.1 より前のバージョンの Cordova (推奨バージョンは 6.1.1 ) を使用する場合は、config.xml で Toast Capable フラグを true に設定する必要もあります。

既定の (デバッグ) ビルドでプッシュ通知をサポートするには、build.json ファイルを開きます。"release" 構成をデバッグ構成にコピーします。

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

更新した後、上記のコードは次のようになります。

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

アプリをビルドし、エラーがないことを確認します。これで、クライアント アプリが、モバイル アプリ バックエンドから送信される通知に登録されました。ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。

####Windows アプリでプッシュ通知をテストする

Visual Studio で、**Windows-x64** や **Windows-x86** などの Windows プラットフォームがデプロイ ターゲットとして選択されていることを確認します。Visual Studio をホストしている Windows 10 PC でアプリを実行するには、**[ローカル コンピューター]** を選択します。

[実行] ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。

アプリで新しい todoitem の名前を入力し、プラス (+) アイコンをクリックして追加します。

項目が追加されたときに、通知が受信されていることを確認します。

##<a name="next-steps"></a>次のステップ

* プッシュ通知の詳細については、[Notification Hubs] に関する記事を参照してください。
* まだ認証を追加していない場合は、チュートリアルの続きとして、Apache Cordova アプリに[認証を追加]してください。

SDK の使用方法を確認してください。

* [Apache Cordova SDK]
* [ASP.NET サーバー SDK]
* [Node.js サーバー SDK]

<!-- URLs -->
[認証を追加]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova クイック スタート]: app-service-mobile-cordova-get-started.md
[認証]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google アカウント]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google デベロッパー コンソール]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push のインストール ドキュメント]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/ja-JP/features/cordova-vs.aspx
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET サーバー SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js サーバー SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->