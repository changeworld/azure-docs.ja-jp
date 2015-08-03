<properties
	pageTitle="Cordova/Phonegap 用 Azure Mobile Engagement の使用"
	description="Cordova/Phonegap アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Cordova/Phonegap 用 Azure Mobile Engagement の使用

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使用して、アプリの使用状況を把握する方法、および Cordova を使用して開発されたモバイル アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。

このチュートリアルでは、Mac を使用して空の Cordova アプリを作成してから、Mobile Engagement SDK と統合します。このアプリは、基本的な分析データを収集し、iOS では Apple Push Notification System (APNS)、Android では Google Cloud Messaging (GCM) を使用してプッシュ通知を受信します。このアプリを、テスト用に iOS デバイスまたは Android デバイスにデプロイします。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

このチュートリアルには、次のものが必要です。

+ XCode (iOS へのデプロイ用)。Mac App Store からインストールできます。
+ [Android SDK およびエミュレーター](http://developer.android.com/sdk/installing/index.html) (Android へのデプロイ用)
+ APNS のプッシュ通知証明書 (.p12)。Apple Dev Center から入手できます。
+ GCM 用の GCM プロジェクト番号。Google デベロッパー コンソールから入手できます。
+ [Mobile Engagement 用の Cordoca プラグイン](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Cordova プラグインのソース コードおよび ReadMe ファイルは、[Github](https://github.com/Azure/azure-mobile-engagement-cordova) にあります。

##<a id="setup-azme"></a>アプリ用のモバイル エンゲージメントの設定

1. Azure 管理ポータルにログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリ サービス]**、**[モバイル エンゲージメント]**、**[作成]** の順にクリックします。

   	![][1]

3. 表示されたポップアップに、次の情報を入力します。

   	![][2]

	- **アプリケーション名**: アプリケーションの名前。 
	- **プラットフォーム**: アプリのターゲット プラットフォーム (Cordova アプリのデプロイ先に応じて **iOS** または **Android** を選択します)。
	- **アプリケーション リソース名**: このアプリケーションに API と URL を通じてアクセスするために使用する名前。 
	- **場所**: このアプリとアプリ コレクションがホストされるデータ センター。
	- **コレクション**: 以前に作成したコレクションか [新しいコレクション] を選択します。
	- **コレクション名**: アプリケーションのグループを表します。すべてのアプリを 1 つのグループに含めることで、それらをメトリック集計できます。該当する場合は、ここで会社名や部門を使用する必要があります。

4. **[アプリケーション]** タブで、作成したアプリを選択します。

5. **[接続情報]** をクリックして、モバイル アプリに統合する SDK に組み込む接続設定を表示します。

   	![][3]

6. **[接続文字列]** をコピーします - これは、アプリケーション コード内で、このアプリケーションを識別し、Phone アプリからモバイル エンゲージメント サービスに接続するために必要なものです。

   	![][4]

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。

統合のデモンストレーションを行うために、Cordova で基本的なアプリを作成します。

###新しい Cordova プロジェクトを作成します。

1. Mac で *[ターミナル]* ウィンドウを起動して次を入力し、既定のテンプレートから新しい Cordova プロジェクトを作成します。

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]iOS アプリのデプロイに最終的に使用する発行プロファイルが、App ID として「com.mycompany.myapp」を使用していることを確認します。

2. 次を実行して **iOS** のプロジェクトを構成し、その構成を iOS シミュレーターで実行します。

		$ cordova platform add ios 
		$ cordova run ios

3. 次を実行して **Android** のプロジェクトを構成し、その構成を Android エミュレーターで実行します。

		$ cordova platform add android
		$ cordova run android

4. 	Cordova Console プラグインを追加します。

		$ cordova plugin add cordova-plugin-console 

###アプリをモバイル エンゲージメントのバックエンドに接続する

1. Azure Mobile Engagement Cordova プラグインに変数値を設定しながら、このプラグインをインストールします。

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	次に例を示します。

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]AppId、SDKKey、およびコレクションは、接続文字列 **Endpoint={あなたのアプリ コレクション.ドメイン};SdkKey={あなたの SDKKey};AppId={あなたの AppId}** から取得できます。

##<a id="monitor"></a>リアルタイム監視の有効化

1. Cordova プロジェクトで、**www/js/index.js** を編集して、*deviceReady* イベントが受信された場合に新しいアクティビティを宣言する Mobile Engagement への呼び出しを追加します。

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. アプリケーションを実行します。
		
	- **iOS の場合**
	
		[`Terminal`] ウィンドウで次を実行して、新しいシミュレーター インスタンスでアプリを起動します。

			cordova run ios

	- **Android の場合**
		
		[`Terminal`] ウィンドウで次を実行して、新しいエミュレーター インスタンスでアプリを起動します。

			cordova run android

3. コンソールのログでは、次のことを確認できます。

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###アプリがリアルタイム監視に接続していることを確認する

このセクションでは、モバイル エンゲージメントのリアルタイム監視機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続していることを確認する方法を説明します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [**関与**] ボタンをクリックします。

	![][6]

2. アプリの **Engagement ポータル**の設定ページが表示されます。**[監視]** タブをクリックします。

	![][7]

3. エミュレーターで実行されているアプリが正しく構成されている場合は、アプリの実行中にリアルタイムで記録されたセッションが表示されます。

	![][8]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーとのやり取りを行うことができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するためにアプリをセットアップします。

###Mobile Engagement のプッシュの資格情報を設定する

Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に Apple iOS 証明書または GCM サーバーの API キーへのアクセスを許可する必要があります。
	
1. モバイル エンゲージメント ポータルに移動します。このプロジェクト用に使用しているアプリを対象にしていることを確認し、下部にある **[エンゲージ]** ボタンをクリックします。
	
	![][10]
	
2. Engagement ポータルの設定ページが表示されます。ここで、**[ネイティブ プッシュ通知]** セクションをクリックします。
	
	![][11]

3. iOS の証明書/GCM サーバーの API キーの設定

	**(iOS)**

	a..p12 を選択してアップロードし、パスワードを入力します。
	
	![][12]

	**(Android)**

	a.次に示す [GCM 設定] セクションの **[API キー]** の編集アイコンをクリックします。
		
	![][20]
	
	b.ポップアップで GCM サーバーのキーを貼り付けて、**[OK]** をクリックします。
	
	![][21]

###Cordova アプリでのプッシュ通知の有効化

**www/js/index.js** を編集して、プッシュ通知の要求とハンドラーの宣言を行う Mobile Engagement への呼び出しを追加します。

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###アプリの実行

**(iOS)**

1. iOS では実在するデバイスへのプッシュ通知だけが許可されているため、XCode を使用してアプリをビルドし、デバイスにデプロイして、プッシュ通知をテストします。Cordova プロジェクトを作成した場所の **...\platforms\ios** に移動します。XCode でネイティブ .xcodeproj ファイルを開きます。 
	
2. 先ほど Mobile Engagement ポータルにアップロードした証明書と、Cordova アプリの作成中に指定した App Id を含むプロビジョニング プロファイルが設定されたアカウントを使用して、Cordova アプリをビルドして iOS デバイスにデプロイします。XCode で **Resources*-info.plist** ファイルの *Bundle ID* をチェックアウトして、照合することができます。

3. デバイスに、アプリが通知を送信する許可を要求したことを示す標準の iOS ポップアップが表示されます。送信を許可します。

**(Android)**

GCM 通知は Android エミュレーターでサポートされているため、エミュレーターを使用するだけで Android アプリを実行することができます。

	cordova run android

##<a id="send"></a>アプリへ通知を送信する

デバイスで実行されているアプリにプッシュ通知を送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. Mobile Engagement ポータルで [リーチ] タブに移動します

2. **[新しいお知らせ]** をクリックして、プッシュ キャンペーンを作成します。

	![][13]

3. 入力情報を指定してキャンペーンを作成します。

	![][14]

	- 	キャンペーンの名前を指定します。 
	- 	**(Android)** **[配信タイプ]** として *[システム通知]* - *[簡易]* を選択します。
	- 	配信時刻は次のように選択します。 
		- 	**IOS** の場合: *[アプリ外のみ]*
		- 	**Android** の場合: *[任意の時刻]*
		
		これは、テキストを扱う単純な種類のプッシュ通知です。
	- 	通知のテキストに、プッシュ通知の最初の行に表示されるタイトルをまず入力します
	- 	次に、次の行に表示されるメッセージを入力します

4. スクロール ダウンし、コンテンツ セクションで [**通知のみ**] を選択します。

	![][15]

5. (オプション) アクション URL を指定することもできます。この URL では、プラグインの **AZME REDIRECT URL** 変数を設定する際に提供された URL スキーム (*myapp://test*) を使用するようにしてください。

5. 最も基本的なキャンペーンの設定が完了したので、再度下にスクロールし、キャンペーンを**作成**して保存します。
	
	![][16]

6. 最後に、キャンペーンを**アクティブ化**します。
	
	![][17]

7. このキャンペーンでは、デバイスまたはエミュレーターにプッシュ通知が表示されるはずです。

##<a id="next-steps"></a>次のステップ
[Cordova Mobile Engagement SDK で使用できるすべてのメソッドの概要](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO4-->