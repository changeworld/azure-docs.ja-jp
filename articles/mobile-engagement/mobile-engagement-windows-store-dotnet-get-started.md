<properties
	pageTitle="Windows ユニバーサル アプリの Azure Mobile Engagement の概要"
	description="Windows ユニバーサル アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Windows ユニバーサル アプリの Azure Mobile Engagement の概要

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、アプリの使用状況を理解して、プッシュ通知を Windows ユニバーサル アプリケーションのセグメント化されたユーザーに送信するために、Azure Mobile Engagement の使用方法を説明します。このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。基本的なアプリ使用データを収集し、Windows 通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ユニバーサル アプリを作成します。

このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget パッケージ

> [AZURE.IMPORTANT]このチュートリアルを完了することは、Windows ユニバーサル アプリの他のすべての Mobile Engagement チュートリアルの前提条件です。このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>をご覧ください。

##<a id="setup-azme"></a>Windows ユニバーサル アプリの Mobile Engagement を設定する

[AZURE.INCLUDE [ポータルで Mobile Engagement アプリを作成する](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。完全な統合のドキュメントについては、「[Mobile Engagement Windows ユニバーサル SDK の統合](../mobile-engagement-windows-store-sdk-overview/)」を参照してください。

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

###新しい Windows ユニバーサル アプリ プロジェクトを作成する

次の手順では、以前のバージョンの Visual Studio と類似した手順で、Visual Studio 2015 を使用します。

1. Visual Studio を起動し、**ホーム**画面で **[新しいプロジェクト]** を選択します。

2. ポップアップ ウィンドウで **[Windows 8]**、**[ユニバーサル]**、**[空のアプリケーション (ユニバーサル Windows 8.1)]** を選択します。アプリの **[名前]** と **[ソリューション名]** を入力し、**[OK]** をクリックします。

    ![][1]

> [AZURE.IMPORTANT]Azure Mobile Engagement では、まだ Windows 10 ユニバーサル Windows アプリがサポートされていません。

これで、Azure Mobile Engagement SDK を統合する新しい Windows ユニバーサル アプリ プロジェクトが作成されました。

###アプリを Mobile Engagement のバックエンドに接続する

1. プロジェクトに [MicrosoftAzure.MobileEngagement] Nuget パッケージをインストールします。Windows と Windows Phone の両方のプラットフォームを対象としている場合、両方のプロジェクトにこれを行う必要があります。同じ Nuget パッケージが各プロジェクトに適切なプラットフォーム固有のバイナリを配置します。

2. **Package.appxmanifest** を開き、そこに次の機能を追加します。

		Internet (Client)

	![][2]

3. 先にコピーした Mobile Engagement アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間にコピーします。

	![][3]

	>[AZURE.TIP]アプリの対象プラットフォームを Windows と Windows Phone の両方にする場合、サポートされているプラットフォームごとに 1 つ、2 つの Mobile Engagement アプリケーションを作成する必要があります。そうすることで、対象ユーザーを適切にセグメント化し、プラットフォームに合わせた通知を送信できます。

4. `App.xaml.cs` ファイルで次の操作を行います。

	a.`using` ステートメントを追加します。

			using Microsoft.Azure.Engagement;

	b.**OnLaunched** メソッドで SDK を初期化します。

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c.**OnActivated** メソッドに次を挿入します。このメソッドがない場合は追加します。

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>リアルタイム監視の有効化

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. 	**MainPage.xaml.cs** に、次の `using` ステートメントを追加します。

		using Microsoft.Azure.Engagement.Overlay;

2. **MainPage** の基本クラスの **Page** を **EngagementPageOverlay** に置き換えます。

		class MainPage : EngagementPageOverlay

3. `MainPage.xaml` ファイルで次の操作を行います。

	a.次の内容を名前空間宣言に追加します。

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

	b.XML タグ名の **Page** を **engagement:EngagementPageOverlay** に置き換えます。
	
> [AZURE.IMPORTANT]ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。これは既定のテンプレートに `OnNavigatedTo` メソッドがある Windows Phone プロジェクトで特に重要です。

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーに情報を提供できます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

1. `Package.appxmanifest` ファイルの **[アプリケーション]** タブの **[通知]** で **[トースト対応:]** に **[はい]** を設定します。

	![][5]

###REACH SDK を初期化する

1. `App.xaml.cs` で、エージェント初期化の直後に **OnLaunched** 関数で **EngagementReach.Instance.Init();** を呼び出します。

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. `App.xaml.cs` で、エージェント初期化の直後に **OnActivated** 関数で **EngagementReach.Instance.Init(e);** を呼び出します。

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

これでトーストを送信する準備ができました。それでは、この基本的な統合が正しく設定されているかどうかを検証してみましょう。

###通知を送信するために Mobile Engagement へのアクセスを許可する

1. アプリを Windows ストア アプリに関連付け、**パッケージ セキュリティ ID (SID)** と**シークレット キー** (クライアント シークレット) を取得する必要があります。[Windows ストア デベロッパー センター]からアプリを作成し、Visual Studio から**アプリとストアを関連付ける**ことができます。

	![][7]

2. Mobile Engagement ポータルの **[設定]** に移動し、左側の **[ネイティブ プッシュ]** セクションをクリックします。

3. **[編集]** ボタンをクリックし、以下に示すように、**パッケージ セキュリティ ID (SID)** と**シークレット キー**を入力します。

	![][6]

##<a id="send"></a>アプリへ通知を送信する

[AZURE.INCLUDE [Windows Push キャンペーンを作成する](../../includes/mobile-engagement-windows-push-campaign.md)]

アプリが実行中の場合は、アプリ内通知が表示されます。アプリが閉じている場合は、トースト通知が表示されます。トースト通知ではなくアプリ内通知が表示され、Visual Studio のデバッグ モードでアプリを実行している場合、ツールバーの **[ライフサイクル イベント] -> [中断]** を選択し、アプリが実際に中断していることを確認します。Visual Studio でアプリケーションをデバッグ中に [ホーム] ボタンをクリックしただけでは中断されず、アプリ内通知は表示されても、トースト通知は表示されないことがあります。

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows ストア デベロッパー センター]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png

<!---HONumber=Oct15_HO3-->