<properties
	pageTitle="Azure Mobile Engagement の使用"
	description="Azure Mobile Engagement を使用して、Android アプリの分析やプッシュ通知を送信する方法について説明します。"
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
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Android アプリ用 Azure Mobile Engagement の使用

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を理解し、Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。このシナリオでは、基本的なデータを収集する空の Android アプリを作成し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信します。

このチュートリアルには、次のものが必要です。

+ Android SDK (Android Studio を使うことを前提とします)。[ここ](http://go.microsoft.com/fwlink/?LinkId=389797)からダウンロードできます。
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]このチュートリアルを完了することは Android アプリ用の他のすべての Mobile Engagement のチュートリアルを実行するための前提条件であり、これを完了するにはアクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

##<a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する

[AZURE.INCLUDE [ポータルで Mobile Engagement アプリを作成する](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントについては、「[Mobile Engagement Android SDK の統合](../mobile-engagement-android-sdk-overview/)」を参照してください。

統合の例を示すために、Android Studio で基本的なアプリを作成します。

###新しい Android プロジェクトを作成する

1. **Android Studio** を開始し、ポップアップ画面で **[Start a new Android Studio project]** を選択します。

    ![][1]

2. アプリ名と会社のドメインを入力します。後で使用するために入力内容をメモします。**[次へ]** をクリックします。

    ![][2]

3. ターゲット フォーム ファクターと API レベルを選び、**[Next]** をクリックします。
	
	>[AZURE.NOTE]Mobile Engagement では、最小で API レベル 10 (Android 2.3.3) が必要です。

    ![][3]

4. ここでこのアプリの唯一の画面である **[Blank Activity]** を選択し、**[Next]** をクリックします。

    ![][4]

5. 最後に、既定はそのままにして **[Finish]** をクリックします。

    ![][5]

Mobile Engagement を統合するデモ アプリが Android Studio によって作成されます。

###SDK ライブラリをプロジェクトに含める

SDK ライブラリをダウンロードして統合する

1. [Mobile Engagement Android SDK] をダウンロードします。
2. アーカイブ ファイルをコンピューターのフォルダーに抽出します。
3. SDK の現在のバージョン用の .jar ライブラリを探し、クリップボードにコピーします。

	  ![][6]

4. **[Project]** セクション (1) に移動し、[libs] フォルダー (2) に .jar を貼り付けます。

	  ![][7]

5. ライブラリを読み込んでプロジェクトを最新にします。

	  ![][8]

###接続文字列を使用してアプリを Mobile Engagement のバックエンドに接続します。

1. 次のコード行をアクティビティ作成の中にコピーします (アプリケーションの 1 か所でのみ実行する必要があります。通常はメイン アクティビティの中で実行します)。このサンプル アプリの MainActivity を [src]、[main]、[java] フォルダーから開き、次を追加します。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Alt キーを押しながら Enter を押すか、次の import ステートメントを追加して、参照を解決します。

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. アプリの [**接続情報**] ページで Azure ポータルに戻り、[**接続文字列**] をコピーします。

	  ![][9]

4. 次の例に示すように、`setConnectionString` パラメーターに貼り付けて設定を置き換えます。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###アクセス権限とサービス宣言を追加する

1. 次のアクセス権限をプロジェクトの Manifest.xml の `<application>` タグの直前または直後に追加します。

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. 次の設定を `<application>`タグと`</application>` タグの間に追加して、エージェント サービスを宣言します。

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. 貼り付けたばかりのコードの label 内の `"<Your application name>"` をアプリケーション名に置き換えます。この内容は **[設定]** メニューに表示されます。デバイスで実行されているサービスをユーザーはここで確認できます。たとえば、このラベルに「サービス」という語句を追加できます。

###画面を Mobile Engagement に送信する

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

**MainActivity.java** に移動して、**MainActivity** の基本クラスを **ActionBarActivity** から **EngagementActivity** に置き換えます。

	public class MainActivity extends EngagementActivity {

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、プッシュ通知とアプリ内メッセージングを利用して、ユーザーにキャンペーン情報を提供できます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

### アプリ内メッセージングの有効化

1. Manifest.xml の `<application>` タグと `</application>` タグの間に、次のアプリ内メッセージングのリソースをコピーします。

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

		 ![][13]

	2. Android Studio に戻り、プロジェクト ファイルの 'main' ディレクトリを選択して貼り付け、リソースをプロジェクトに追加します。

		 ![][14]

###通知のアイコンを指定します。

次の XML スニペットを Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

これにより、システムおよびアプリ内通知の両方に表示されるアイコンが定義されます。これはアプリ内通知では任意ですが、システム通知では必須です。Android では無効なアイコンが設定されたシステム通知は拒否されます。

いずれかの**描画可能な**フォルダーにあるアイコン (``engagement_close.png`` など) を使用してください。**mipmap** フォルダーはサポートされていません。

>[AZURE.NOTE]**ランチャー** アイコンは使用しないでください。このアイコンの解像度は異なっており、通常はサポート対象外の mipmap フォルダー内にあります。

実際のアプリでは、[Android の設計ガイドライン](http://developer.android.com/design/patterns/notifications.html)に従って、通知に適したアイコンを使用します。

>[AZURE.TIP]適切な解像度のアイコンを使用するには、[これらの例](https://www.google.com/design/icons)を参考にしてください。**[Notification]** セクションまでスクロールして 1 つのアイコンをクリックし、`PNGS` をクリックして描画可能なアイコンのセットをダウンロードします。アイコンのバージョンごとに、使用する描画可能なフォルダーと解像度を確認できます。

###GCM のプッシュ通知を受信するようにアプリを設定する

1. Google Play コンソールから取得した `project number` を置き換えた後、Manifest.xml の `<application>` タグと `</application>` タグ間に次を貼り付けます。\\n は意図的に付けられています。プロジェクト番号の末尾には必ずこれを付けてください。

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

3. 強調表示された最新のアクセス権限セットを、`<application>` タグの前に追加します。`<Your package name>` をアプリケーションの実際のパッケージ名で置き換えます。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Mobile Engagement に GCM API キーへのアクセス権限を付与する

Mobile Engagement にプッシュ通知の送信を許可するには、API キーへのアクセス権限を付与する必要があります。これは、キーを構成して Mobile Engagement ポータルに入力することで実行します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の **[関与]** ボタンをクリックします。

	![][15]

2. ここで **[設定]**、**[ネイティブ プッシュ通知]** セクションをクリックし、GCM キーを入力します。
	  
	![][16]

3. 次に示す **[GCM 設定]** セクションの **[API キー]** の**編集**アイコンをクリックします。
	  
	![][17]

4. ポップアップ画面で、「[Google Cloud Messaging を有効にする](#register)」セクションで取得した GCM サーバー キーを貼り付け、**[OK]** をクリックします。

	![][18]

##<a id="send"></a>アプリへ通知を送信する

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. Mobile Engagement ポータルで [**リーチ**] タブに移動します。
	 
2. **[新しいお知らせ]** をクリックし、プッシュ通知キャンペーンを作成します。
	 
	![][20]

3. 次の手順に従い、キャンペーンの最初のフィールドをセットアップします。
	 
	![][21]

	a.キャンペーンの名前を付けます。

	b.**[配信タイプ]** として *[システム通知]、[簡易]* を選択します。これは、タイトルと数行のテキストを表示する単純な Android のプッシュ通知です。

	c.**[配信時刻]** で *[指定なし]* を選択し、アプリが起動されているかどうかに関係なく、アプリが通知を受信できるようにします。

	d.通知テキストに、プッシュ通知内で太字で表示される**タイトル**を入力します。

	e.次に、**メッセージ**を入力します。

4. 下にスクロールし、**[コンテンツ]** セクションで **[通知のみ]** を選択します。

	![][22]

5. 最も基本的なキャンペーンの設定が完了しました。もう一度下にスクロールし、**[作成]** ボタンをクリックしてキャンペーンを保存します。

6. 最後の手順として、**[アクティブ化]** をクリックしてキャンペーンをアクティブにし、プッシュ通知を送信します。
    
	![][24]

<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=Sept15_HO4-->