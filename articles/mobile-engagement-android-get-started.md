<properties 
	pageTitle="Azure Mobile Engagement を使い始める" 
	description="Azure Mobile Engagement を使って分析とプッシュ通知を行う方法について説明します。" 					services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Mobile Engagement を使い始める

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

このトピックでは、Azure Mobile Engagement を使ってアプリの使用状況を理解し、Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法を示します。 
このチュートリアルでは、Mobile Engagement を使った簡単なブロードキャスト シナリオについて説明します。このシナリオでは、基本的なデータを収集する空の Android アプリを作成し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信します。完了すると、すべてのデバイスまたはデバイスのプロパティに基づく特定のターゲット ユーザーにプッシュ通知をブロードキャストすることができます。次のチュートリアルで、Mobile Engagement を使って特定のユーザーとデバイス グループに注目する方法を確認してください。


このチュートリアルには、次のものが必要です。

+ Android SDK (Android Studio を使うことを前提とします)。[ここ]からダウンロードできます。(http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT] このチュートリアルを完了することは Android アプリ用の他のすべての Mobile Engagement のチュートリアルを実行するための前提条件であり、これを完了するにはアクティブな Azure アカウントが必要です。アカウントを持っていない場合は、無料のトライアル アカウントをほんの 2、3 分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>アプリ用の Mobile Engagement を設定する

1. [Azure 管理ポータル] にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Mobile Engagement]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されたポップアップ画面で、次の情報を入力します。
 
   	![][8]

	1. **アプリケーション名**: アプリケーションの名前を入力することができます。任意の文字を使うことができます。
	2. **プラットフォーム**:アプリのターゲット プラットフォームを選択します (アプリが複数のプラットフォームをターゲットとする場合は、各プラットフォームに対してこのチュートリアルを繰り返します)
	3. **アプリケーション リソース名**: この名前を使用して、API および URL 経由でこのアプリケーションにアクセスします。標準の URL 文字のみを使用してください: 自動的に生成された名前を使うのが確実な方法です。また、この名前は一意である必要があるため、名前の競合を避けるため、プラットフォーム名を追加することをお勧めします。
	4. **場所**: このアプリ (またさらに重要なものとしてそのコレクション (以下を参照)) をホストするデータ センターを選択します。
	5. **コレクション**:アプリケーションを既に作成している場合は、以前に作成したコレクションを選びます。それ以外の場合は新しいコレクションを選びます。
	6. **コレクション名**: これは、アプリケーションのグループを表します。すべてのアプリが集計を計算できるグループに含まれることも保証します。会社名や部門を使うことを強くお勧めします。


	入力が終わったら、チェック ボタンをクリックしてアプリの作成を終了します。

4. **[Application]** タブで作成したアプリをクリック/選択します。
 
   	![][9]

5. **[Connection Info]** をクリックし、SDK 統合にプットする接続設定を表示します。
 
   	![][10]

6. 最後に、**接続文字列**をメモします。これは、アプリケーション コードからこのアプリを識別するために必要な情報です。

   	![][11]

	>[AZURE.TIP] 接続文字列の右側の "コピー" アイコンを使って、クリップボードにコピーすると便利です。

##<a id="connecting-app"></a>Mobile Engagement バックエンドにアプリを接続する

このチュートリアルでは、データの収集とプッシュ通知の送信を行うために必要な最小限の設定である "基本的な統合" を示しています。統合に関する完全なドキュメントについては、[Mobile Engagement Android SDK ドキュメント]を参照してください。

統合の例を示すために、Android Studio で基本的なアプリを作成します。

###新しい Android プロジェクトを作成する

既にアプリがあり、Android の開発に慣れている場合は、この手順をスキップできます。

1. Android Studio を起動し、ポップアップ画面で **[Start a new Android Studio project]** をクリックします。

   	![][12]

2. アプリ名と会社のドメインを入力します。後で必要になるため、これらをメモした後、**[Next]** をクリックします。

   	![][13]

3. 次に、ターゲット フォーム ファクターと API レベルを選び、**[Next]** をクリックします。 

	>[AZURE.NOTE] Mobile Engagement では、最小で API レベル 10 (Android 2.3.3) が必要です。

   	![][14]

4. これで、作成する単純なアプリに、メイン画面であり唯一の画面となるアクティビティを追加できるようになりました。**[Blank Activity]** が選ばれていることを確認し、**[Next]** をクリックします。

   	![][15]

5. このチュートリアルでは、ウィザードの最後の画面では何もせずに **[Finish]** をクリックできます。

   	![][16]

Mobile Engagement を統合するデモ アプリが Android Studio によって作成されます。

###プロジェクトに SDK ライブラリを含める

SDK ライブラリのダウンロードと統合

1. [Mobile Engagement Android SDK] をダウンロードします。
2. アーカイブ ファイルをコンピューターのフォルダーに抽出します。
3. SDK の現在のバージョン用の .jar ライブラリを識別し、クリップボードにコピーします (このドキュメントは SDK 3.0.0 バージョン用に作成されています) 。

	![][17]

4. [プロジェクト] セクション (1) に移動し、[libs] フォルダー (2) に .jar を貼り付けます。

	![][18]

5. ライブラリを読み込んでプロジェクトを最新にします。

	![][19]


###接続文字列を使ってアプリを Mobile Engagement バックエンドに接続します。

1. 次のコード行をアクティビティ作成の中にコピーします (アプリケーションの 1 か所でのみ実行する必要があります。通常はメイン アクティビティの中で実行します)。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Azure ポータルに戻り、アプリの **[接続情報]** ページの **[接続文字列]** をコピーします。

	![][11]

3. 次の例に示すように、`setConnectionString` パラメーターに貼り付けて設定を置き換えます (AppId と Sdkkey は非表示になっています)。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration と EngagementAgent が高い確率で未解決になります (コード内に赤で示されます)。未解決のクラスをクリックし、Alt + Enter キーを押して、それらを自動的に解決します。

	![][20]

###アクセス権限とサービス宣言を追加する

1. 次のアクセス権限を、プロジェクトの Manifest.xml の `<application>` タグの直前に追加します。
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. 次の設定を <application> タグと </application> タグの間に追加して、エージェント サービスを宣言します。

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. 貼り付けたばかりのコードの label 内の < Your application name> をアプリケーション名に置き換えます。次に例を示します。

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###画面を Mobile Engagement に送信する

データ送信を開始し、ユーザーがアクティブであることを確認するためには、Mobile Engagement バックエンドに少なくとも 1 つの画面 (アクティビティ) を送信する必要があります。これは、SDK に用意されている EngagementActivity でアクティビティをサブクラス化することで実現します。
これを行うためには、次のように ActionBarActivity の前にある MainActivity のスーパークラスを次のように書き換えます。

![][22]

>[AZURE.NOTE] クラスが赤で表示される場合は、それをクリックし、Alt + Enter キーを押して解決することを忘れないでください。

##<a id="monitor"></a>リアル タイム監視機能によりアプリの接続状況を確認する方法

このセクションでは、Mobile Engagement のリアル タイム監視機能を使用して、アプリが Mobile Engagement に接続していることを確認する方法を説明します。

1. Mobile Engagement ポータルに移動します。

	Azure ポータルで、このプロジェクトのために使っているアプリが選ばれていることを確認し、下部にある **[関与]** ボタンをクリックします。

	![][26]

2. アプリ用の Engagement ポータルの設定ページが表示されます。次に示す **[監視]**] タブをクリックします。
	![][30]

3. モニターは、アプリを起動するデバイスをリアルタイムで表示する準備ができています。
	![][31]

4. Android Studio に戻り、緑の三角形をクリックしてデバイスを選ぶことで、モニター内または接続されたデバイス内でアプリを起動します。
	![][32]

5. 正常に動作した場合は、モニターに 1 つのセッションが表示されます。 
	![][33]

**お疲れさまでした。**これで、このチュートリアルの最初の手順が完了し、アプリが Mobile Engagement バックエンドに接続し、既にデータを送信しています。


##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージを有効にする

Mobile Engagement では、キャンペーンの一環としてプッシュ通知とアプリ内メッセージを使ってユーザーにリーチできます。このモジュールは、Mobile Engagement ポータルでは "リーチ" と呼ばれます。
この後のセクションで、それらを受信するようにアプリを設定します。

### アプリ内メッセージの有効化

1. Manifest.xml の < application > タグと </application > タグの間に、次のアプリ内メッセージ リソースをコピーします。

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
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. 次の手順に従って、プロジェクトにリソースをコピーします。
	1. SDK ダウンロード コンテンツに戻り、 'res' フォルダーを開きます。
	2. 2 つのフォルダーを選び、それらをクリップボードにコピーします。

		![][23]

	4. Android Studio に戻り、プロジェクトの  'res' 部分を選び、リソースを貼り付けてプロジェクトに追加します。

		![][24]

###通知の既定のアイコンを指定する
次のコードは、通知と一緒に表示する既定のアイコンを定義します。ここでは、Android Studio によって作成されるプロジェクトと一緒に提供されるアイコンを使います。この xml スニペットを Manifest.xml の < application > タグと </application > タグの間に貼り付けます。

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###アプリが GCM のプッシュ通知を受信できるようにする

1. gcm:sender メタデータをコピーして、Manifest.xml の < application > タグと </application > タグの間に貼り付けます。下の非表示の値 (* が付いています) は、Google Play コンソールから取得した `project number`です。

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 次のコードを、Manifest.xml の < application > タグと </application > タグの間に貼り付けます。`<category android:name="com.mycompany.mysuperapp" />` では、プロジェクトのパッケージ名を使用していることに注意してください。実稼動プロジェクトでは異なる値になります。

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
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. 次に強調表示する最新のアクセス権限セットを、<application> タグの前に追加します。ここでも、実稼働アプリケーションでは置き換える必要があるプロジェクトのパッケージ名を使っています。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Mobile Engagement に GCM API キーへのアクセス権限を与える

Mobile Engagement がプッシュ通知を送信できるようにするには、API キーへのアクセス権限を与える必要があります。これは、キーを構成して Mobile Engagement ポータルに入力することで実行します。

1. Mobile Engagement ポータルに移動します。

	Azure ポータルで、このプロジェクトのために使っているアプリが選ばれていることを確認し、下部にある **[関与]** ボタンをクリックします。

	![][26]

2. Engagement ポータルの設定ページが表示されます。ここで、GCM キーを入力するために **[ネイティブ プッシュ通知]** をクリックします。
	![][27]

3. 次に示す [GCM 設定] セクションの **[API キー]** の編集アイコンをクリックします。
	![][28]

4. ポップアップ画面で、「[Google Cloud Messaging を有効にする]」セクションで取得した GCM サーバー キーを貼り付け、(#register) **[OK]** をクリックします。

	![][29]

これですべての設定が終わりました。次に、この基本的な統合が正しく行われたことを確認します。

> [AZURE.IMPORTANT] この新しいコードをビルドして起動し、アプリを終了した後、次の手順を実行する前に約 1 分間待機してください。

##<a id="send"></a>アプリケーションに通知を送信する方法

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. Mobile Engagement ポータルの **[リーチ]** タブに移動します。
	![][34]

2. プッシュ キャンペーンを作成するために **[新しいアナウンスメント]** をクリックします。
	![][35]

3. 次の手順に従って、キャンペーンの最初のフィールドを設定します。
	![][36]

	1. キャンペーンに任意の名前を付けます。
	2. **配信タイプ** として [ *System notification / Simple*] を選びます。これは、タイトルと数行のテキストを表示する単純な Android のプッシュ通知です。
	3. **配信時刻**として [ *Any time*] を選び、アプリが起動されているかどうかに関係なく通知を受け取ることができるように設定します。
	4. 通知テキストとして、プッシュ通知内に太字で表示されるタイトルを入力します。
	5. 次に、メッセージを入力します。

4. 下へスクロールし、コンテンツ セクションで **[通知のみ]** を選びます。
	![][37]

5. これで、最も基本的なキャンペーンの設定が完了しました。さらに下へスクロールし、キャンペーンを作成して保存します。
![][38]

6. 最後のステップとして、キャンペーンをアクティブ化します。
![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK に関するドキュメント]: http://go.microsoft.com/?linkid=9874682
[Azure Management ポータル]: https://manage.windowsazure.com

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

<!--HONumber=47-->
