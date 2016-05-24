<properties
	pageTitle="Azure Mobile Engagement の使用"
	description="Azure Mobile Engagement を使用して、Android アプリの分析やプッシュ通知を送信する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Android アプリ用 Azure Mobile Engagement の使用

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を理解し、Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。このシナリオでは、基本的なデータを収集する空の Android アプリを作成し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信します。

## 前提条件

このチュートリアルを完了するには、Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools](https://developer.android.com/sdk/index.html) が必要です。

また、[Mobile Engagement Android SDK](https://aka.ms/vq9mfn) も必要です。

> [AZURE.IMPORTANT] このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)を参照してください。

## Android アプリ用の Mobile Engagement の設定

[AZURE.INCLUDE [ポータルで Mobile Engagement アプリを作成する](../../includes/mobile-engagement-create-app-in-portal.md)]

## アプリを Mobile Engagement のバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントについては、「[Mobile Engagement Android SDK の統合](mobile-engagement-android-sdk-overview.md)」を参照してください。

統合の例を示すために、Android Studio で基本的なアプリを作成します。

### 新しい Android プロジェクトを作成する

1. **Android Studio** を開始し、ポップアップ画面で **[Start a new Android Studio project]** を選択します。

    ![][1]

2. アプリ名と会社のドメインを入力します。後で使用するために入力内容をメモします。**[次へ]** をクリックします。

    ![][2]

3. ターゲット フォーム ファクターと API レベルを選び、**[Next]** をクリックします。

	>[AZURE.NOTE] Mobile Engagement では、最小で API レベル 10 (Android 2.3.3) が必要です。

    ![][3]

4. ここでこのアプリの唯一の画面である **[Blank Activity]** を選択し、**[Next]** をクリックします。

    ![][4]

5. 最後に、既定はそのままにして **[Finish]** をクリックします。

    ![][5]

Mobile Engagement を統合するデモ アプリが Android Studio によって作成されます。

### SDK ライブラリをプロジェクトに含める

1. [Mobile Engagement Android SDK] をダウンロードします。
2. アーカイブ ファイルをコンピューターのフォルダーに抽出します。
3. SDK の現在のバージョン用の .jar ライブラリを探し、クリップボードにコピーします。

	  ![][6]

4. **[Project]** セクション (1) に移動し、[libs] フォルダー (2) に .jar を貼り付けます。

	  ![][7]

5. ライブラリを読み込んでプロジェクトを最新にします。

	  ![][8]

### 接続文字列を使用してアプリを Mobile Engagement のバックエンドに接続します。

1. 次のコード行をアクティビティ作成の中にコピーします (アプリケーションの 1 か所でのみ実行する必要があります。通常はメイン アクティビティの中で実行します)。このサンプル アプリの MainActivity を [src]、[main]、[java] フォルダーから開き、次を追加します。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Alt キーを押しながら Enter を押すか、次の import ステートメントを追加して、参照を解決します。

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. アプリの **[接続情報]** ページで Azure クラシック ポータルに戻り、**[接続文字列]** をコピーします。

	  ![][9]

4. 次の例に示すように、`setConnectionString` パラメーターに貼り付けて設定を置き換えます。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### アクセス権限とサービス宣言を追加する

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

### 画面を Mobile Engagement に送信する

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

**MainActivity.java** に移動して、**MainActivity** の基底クラスを **EngagementActivity** に置き換えます。

	public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] 基底クラスが *Activity* ではない場合、別のクラスからの継承方法について、[高度な Android レポート](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)に関するページを参照してください。


この簡単なサンプル シナリオでは、次の行をコメント アウト (除外) する必要があります。

    // setSupportActionBar(toolbar);

これを残しておきたい場合は、[高度な Android レポート](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)に関するページを確認してください。

## リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、プッシュ通知とアプリ内メッセージングを利用して、ユーザーにキャンペーン情報を提供できます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

### SDK リソースをプロジェクトにコピーする

1. SDK ダウンロード コンテンツに戻り、**res** フォルダーをコピーします。

	![][10]

2. Android Studio に戻り、プロジェクト ファイルの **main** ディレクトリを選択して貼り付け、リソースをプロジェクトに追加します。

	![][11]

[AZURE.INCLUDE [Google Cloud Messaging を有効にする](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [アプリ内メッセージングの有効化](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## 次のステップ

[Android SDK](mobile-engagement-android-sdk-overview.md) に関するページに移動し、SDK の統合について詳細を確認します。

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
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0518_2016-->