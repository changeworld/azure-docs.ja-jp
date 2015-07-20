<properties
	pageTitle="Azure モバイル エンゲージメントの使用"
	description="Android アプリ の分析やプッシュ通知で Azure モバイル エンゲージメントを使用する方法を説明します。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="get-started-article" 
	ms.date="05/01/2015"
	ms.author="piyushjo" />

# Android アプリの Azure Mobile Engagement を開始する

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使ってアプリの使用状況を理解し、Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法を示します。このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。このシナリオでは、基本的なデータを収集する空の Android アプリを作成し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信します。完了すると、デバイス プロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに、プッシュ通知をブロードキャストできるようになります。モバイル エンゲージメントを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。


このチュートリアルには、次のものが必要です。

+ Android SDK (Android Studio を使うことを前提とします)。[ここ](http://go.microsoft.com/fwlink/?LinkId=389797)からダウンロードできます。
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]このチュートリアルを完了することは Android アプリ用の他のすべての Mobile Engagement のチュートリアルを実行するための前提条件であり、これを完了するにはアクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>アプリ用の Mobile Engagement の設定

1. [Azure 管理ポータル](https://manage.windowsazure.com)にログオンし、画面の下部にある **[+新規]** をクリックします。

2. [**アプリ サービス**]、[**モバイル エンゲージメント**]、[**作成**] の順にクリックします。

   	![][7]

3. 表示されたポップアップに、次の情報を入力します。

   	![][8]

	1. **アプリケーション名**: アプリケーションの名前を入力できます。自由に任意の文字を使用してください。
	2. **プラットフォーム**: アプリ向けのターゲット プラットフォームを選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。
	3. **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL 文字のみを使用することをお勧めします。自動生成された名前は、ベースとなる名前として非常に役立ちます。また、この名前は一意である必要があるので、名前の重複を避けるために、プラットフォーム名を付加することをお勧めします。
	4. **場所**: このアプリ (およびさらに重要なそのコレクション) がホストされるデータ センターを選択します (コレクションについては下記を参照)。
	5. **コレクション**: アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は新しいコレクションを選択します。
	6. **コレクション名**。 これは、グループのアプリケーションを表します。すべてのアプリを 1 つのグループに含めることで、それらを集計ができます。会社名や部門名を使用することを強くお勧めします。


	終了したら、チェック ボタンをクリックして、アプリの作成を完了します。

4. [**アプリケーション**] タブで、作成したアプリをクリックして選択します。

   	![][9]

5. 次に、[**接続情報**] をクリックして、統合する SDK に組み込む接続設定を表示します。

   	![][10]

6. 最後に、[**接続文字列**] をメモに取ります。これは、アプリケーション コードからこのアプリを特定する際に必要になります。

   	![][11]

	>[AZURE.TIP]接続文字列の右側にある [コピー] アイコンを使用すると、接続文字列をクリップボードにコピーできるので便利です。

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントについては、[Mobile Engagement Android SDK ドキュメント]を参照してください。

統合の例を示すために、Android Studio で基本的なアプリを作成します。

###新しい Android プロジェクトを作成する

既にアプリがあり、Android の開発に慣れている場合は、この手順をスキップできます。

1. Android Studio を起動し、ポップアップ画面で [**Start a new Android Studio project**] をクリックします。

   	![][12]

2. アプリ名と会社のドメインを入力します。これらは後で必要になるため、書き留めておいてください。次に [**Next**] をクリックします。

   	![][13]

3. 次に、ターゲット フォーム ファクターと API レベルを選び、[**Next**] をクリックします。
	>[AZURE.NOTE]Mobile Engagement では、最小で API レベル 10 (Android 2.3.3) が必要です。

   	![][14]

4. これで、作成する単純なアプリに、メイン画面であり唯一の画面となるアクティビティを追加できるようになりました。[**Blank Activity**] が選ばれていることを確認し、[**Next**] をクリックします。

   	![][15]

5. このチュートリアルでは、ウィザードの最後の画面では何もせずに [**Finish**] をクリックできます。

   	![][16]

Mobile Engagement を統合するデモ アプリが Android Studio によって作成されます。

###SDK ライブラリをプロジェクトに含める

SDK ライブラリをダウンロードして統合する

1. [Mobile Engagement Android SDK] をダウンロードします。
2. アーカイブ ファイルをコンピューターのフォルダーに抽出します。
3. SDK の現在のバージョン用の .jar ライブラリを見つけて、クリップボードにコピーします。

	![][17]

4. [プロジェクト] セクション (1) に移動し、[libs] フォルダー (2) に .jar を貼り付けます。

	![][18]

5. ライブラリを読み込んでプロジェクトを最新にします。

	![][19]


###接続文字列を使用してアプリをモバイル エンゲージメントのバックエンドに接続します。

1. 次のコード行をアクティビティ作成の中にコピーします (アプリケーションの 1 か所でのみ実行する必要があります。通常はメイン アクティビティの中で実行します)。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. アプリの [**接続情報**] ページで Azure ポータルに戻り、[**接続文字列**] をコピーします。

	![][11]

3. 次の例に示すように、`setConnectionString` パラメーターに貼り付けて設定を置き換えます (AppId と Sdkkey は非表示になっています)。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration と EngagementAgent が高い確率で未解決になります (コード内に赤で示されます)。未解決のクラスをクリックし、Alt + Enter キーを押して、それらを自動的に解決します。

	![][20]

###アクセス権限とサービス宣言を追加する

1. 次のアクセス権限をプロジェクトの Manifest.xml の `<application>` タグの直前または直後に追加します。

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	結果は次のようになります。

	![][21]

2. 次の設定を `<application>`タグと`</application>`タグの間に追加して、エージェント サービスを宣言します。

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. 貼り付けたばかりのコードの label 内の `"<Your application name>"` をアプリケーション名に置き換えます。この内容は、ユーザーがデバイスで実行されているサービスを確認することができる [設定] メニューに表示されます。たとえば、このラベルに「サービス」という語句を追加できます。

###画面をモバイル エンゲージメントに送信する

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) をモバイル エンゲージメントのバックエンドに送信する必要があります。これは、SDK に用意されている EngagementActivity でアクティビティをサブクラス化することで実現します。これを行うためには、次のように ActionBarActivity の前にある MainActivity のスーパークラスを次のように書き換えます。

![][22]

>[AZURE.NOTE]クラスが赤で表示される場合は、それをクリックし、Alt + Enter キーを押して解決することを忘れないでください。

##<a id="monitor"></a>アプリがリアルタイム監視に接続していることを確認する方法

このセクションでは、モバイル エンゲージメントのリアルタイム監視機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続していることを確認する方法を説明します。

1. モバイル エンゲージメント ポータルに移動します。

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [**関与**] ボタンをクリックします。

	![][26]

2. アプリのエンゲージメント ポータルの設定ページに移動します。ここで、以下に示す [**モニター**] タブをクリックします。![][30]

3. モニターは、アプリを起動するデバイスをリアルタイムで表示する準備ができています。![][31]

4. Android Studio に戻り、緑の三角形をクリックしてデバイスを選ぶことで、モニター内または接続されたデバイス内でアプリを起動します。![][32]

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。![][33]

**お疲れさまでした。** これで、このチュートリアルの最初の手順が完了し、アプリが Mobile Engagement バックエンドに接続し、既にデータを送信しています。


##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

モバイル エンゲージメントにより、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するためにアプリをセットアップします。

### アプリ内メッセージの有効化

1. Manifest.xml の `<application>` タグと `</application>` タグの間に、次のアプリ内メッセージ リソースをコピーします。

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. 次の手順に従って、プロジェクトにリソースをコピーします。
	1. SDK ダウンロード コンテンツに戻り、'res' フォルダーをコピーします。

		![][23]

	2. Android Studio に戻り、プロジェクト ファイルの ’main’ ディレクトリを選択し、リソースを貼り付けてプロジェクトに追加します。

		![][24]

###通知のアイコンを指定します。

次のコードでは、アプリとシステムの両方で通知の表示に使用されるアイコンを定義します。

アイコンはアプリ内通知では任意ですが、システム通知では必須であり、Android では無効なアイコンが設定されたシステム通知は拒否されます。

この xml スニペットを Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。

いずれかの**描画可能な**フォルダーにあるアイコン (``engagement_close.png`` など) を使用してください。**mipmap** フォルダーはサポートされていません。
	
		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

この例は構文を示すためだけのものですので、[Android の設計ガイドライン](http://developer.android.com/design/patterns/notifications.html)に従って、通知に適したアイコンを使用する必要があります。

ランチャー アイコンは使用しないでください。このアイコンの解像度は異なっており、一般に、サポート対象外の mipmap フォルダー内にあります。

>[AZURE.TIP][これらの例](https://www.google.com/design/icons)を参考にして、適切なアイコンの解像度を使用していることを確認できます。*[Notification]* セクションまでスクロールして 1 つのアイコンをクリックし、`PNGS` をクリックして描画可能なアイコンのセットをダウンロードします。アイコンのバージョンごとの解像度から、どの描画可能なフォルダーを使用すればよいかを確認できます。

###アプリが GCM のプッシュ通知を受信できるようにする

1. gcm:sender メタデータをコピーして、Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。下の非表示の値 (* が付いています) は、Google Play コンソールから取得した `project number` です。\\n は意図的に付けられています。プロジェクト番号の末尾には必ずこれを付けてください。

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 次のコードを、Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。<Your package name> をパッケージ名に置き換えます。

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. `<application>` タグの前に、強調表示する最新のアクセス許可セットを追加します。`<Your package name>` をアプリケーションの実際のパッケージ名で置き換えます。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Mobile Engagement に GCM API キーへのアクセス権限を与える

Mobile Engagement がプッシュ通知を送信できるようにするには、API キーへのアクセス権限を与える必要があります。これは、キーを構成して Mobile Engagement ポータルに入力することで実行します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [**関与**] ボタンをクリックします。

	![][26]

2. Engagement ポータルの設定ページが表示されます。ここで、GCM キーを入力するために [**ネイティブ プッシュ通知**] をクリックします。![][27]

3. 次に示す [GCM 設定] セクションの [**API キー**] の編集アイコンをクリックします。![][28]

4. ポップアップ画面で、「[Google Cloud Messaging を有効にする](#register)」セクションで取得した GCM サーバー キーを貼り付け、[**OK**] をクリックします。

	![][29]

これができたら、次はこの基本統合が正常に行われているかを確認します。

> [AZURE.IMPORTANT]この新しいコードをビルドして起動し、アプリを終了した後、次の手順を実行する前に約 1 分間待機してください。

##<a id="send"></a>アプリへの通知の送信方法

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. Mobile Engagement ポータルの [**リーチ**] タブに移動します。![][34]

2. [**新しいお知らせ**] をクリックして、プッシュ キャンペーンを作成します。![][35]

3. 次の手順に従って、キャンペーンの最初のフィールドをセットアップします。![][36]

	1. キャンペーンに希望する任意の名前を付けます。
	2. [**配信タイプ**] として [*システム通知 / 簡易*] を選択します。これは、タイトルと数行のテキストを表示する単純な Android のプッシュ通知です。
	3. [**配信時刻**] で [*指定なし*] を選択し、アプリが起動されているかどうかに関係なく、アプリが通知を受信できるようにします。
	4. 通知テキストに、プッシュ通知内で太字で表示されるタイトルを入力します。
	5. 次に、メッセージを入力します。

4. スクロール ダウンし、コンテンツ セクションで [**通知のみ**] を選択します。![][37]

5. 最も基本的なキャンペーンの設定が完了したので、もう一度下にスクロールし、キャンペーンを作成して保存します。![][38]

6. 最後の手順で、キャンペーンをアクティブ化します。![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK ドキュメント]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=July15_HO2-->