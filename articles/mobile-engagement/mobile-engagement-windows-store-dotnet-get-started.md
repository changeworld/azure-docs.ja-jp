<properties
	pageTitle="Windows ユニバーサル アプリの Azure Mobile Engagement の概要"
	description="Windows ユニバーサル アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

# Windows ユニバーサル アプリの Azure Mobile Engagement の概要

> [AZURE.SELECTOR]
- [ユニバーサル Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、アプリの使用状況を理解して、プッシュ通知を Windows ユニバーサル アプリケーションのセグメント化されたユーザーに送信するために、Azure Mobile Engagement の使用方法を説明します。このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。基本的なアプリ使用データを収集し、Windows 通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ユニバーサル アプリを作成します。

このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget パッケージ

> [AZURE.IMPORTANT] このチュートリアルを完了することは、Windows ユニバーサル アプリの他のすべての Mobile Engagement チュートリアルの前提条件です。このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>をご覧ください。

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

これで、Azure Mobile Engagement SDK を統合する新しい Windows ユニバーサル アプリ プロジェクトが作成されました。

###アプリを Mobile Engagement のバックエンドに接続する

1. プロジェクトに [MicrosoftAzure.MobileEngagement] Nuget パッケージをインストールします。Windows と Windows Phone の両方のプラットフォームを対象としている場合、両方のプロジェクトにこれを行う必要があります。Windows 8.x および Windows Phone 8.1 の場合は、同じ Nuget パッケージが各プロジェクトに適切なプラットフォーム固有のバイナリを配置します。

2. **Package.appxmanifest** を開き、そこに次の機能を追加します。

		Internet (Client)

	![][2]

3. 先にコピーした Mobile Engagement アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間にコピーします。

	![][3]

	>[AZURE.TIP] アプリの対象プラットフォームを Windows と Windows Phone の両方にする場合、サポートされているプラットフォームごとに 1 つ、2 つの Mobile Engagement アプリケーションを作成する必要があります。そうすることで、対象ユーザーを適切にセグメント化し、プラットフォームに合わせた通知を送信できます。

4. `App.xaml.cs` ファイルで次の操作を行います。

	a.`using` ステートメントを追加します。

			using Microsoft.Azure.Engagement;

	b.エンゲージメントの初期化および設定に専用のメソッドを追加します。

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

			 //... rest of the code
           }

    c.**OnLaunched** メソッドで SDK を初期化します。

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

	c.**OnActivated** メソッドに次を挿入します。このメソッドがない場合は追加します。

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  InitEngagement(e);

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
	
> [AZURE.IMPORTANT] ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。これは既定のテンプレートに `OnNavigatedTo` メソッドがある Windows Phone プロジェクトで特に重要です。

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーに情報を提供できます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

1. `Package.appxmanifest` ファイルの **[アプリケーション]** タブの **[通知]** で **[トースト対応:]** に **[はい]** を設定します。

	![][5]

###REACH SDK を初期化する

`App.xaml.cs` で、エージェント初期化の直後に **InitEngagement** 関数で **EngagementReach.Instance.Init(e);** を呼び出します。

        private void InitEngagement(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

これでトーストを送信する準備ができました。それでは、この基本的な統合が正しく設定されているかどうかを検証してみましょう。

###通知を送信するために Mobile Engagement へのアクセスを許可する

1. Web ブラウザーで [Windows ストア デベロッパー センター]を開き、必要に応じてログインし、アカウントを作成します。
2. 右上隅の **[ダッシュボード]** をクリックし、左側のパネル メニューで **[新しいアプリの作成]** をクリックします。 

	![][9]

2. アプリの名前を予約して、アプリを作成します。

	![][10]

3. アプリが作成されたら、左側のメニューから **[サービス]、[プッシュ通知]** の順に進みます。

	![][11]

4. [プッシュ通知] セクションで、**Live サービス サイト**のリンクをクリックします。

	![][12]

5. プッシュ通知用資格情報セクションに移動します。**[アプリケーション設定]** セクションが表示されていることを確認し、**[パッケージ SID]** と **[クライアント シークレット]** をコピーします。

	![][13]

6. Mobile Engagement ポータルの **[設定]** に移動し、左側の **[ネイティブ プッシュ]** セクションをクリックします。次に、**[編集]** ボタンをクリックし、次に示すように、**[パッケージ セキュリティ ID (SID)]** と **[シークレット キー]** に入力します。

	![][6]

8. 最後に、アプリ ストアに作成されたこのアプリに Visual Studio アプリケーションが関連付けます。これを行うには、Visual Studio で **[アプリケーションをストアと関連付ける]** をクリックする必要があります。

	![][7]

##<a id="send"></a>アプリへ通知を送信する

[AZURE.INCLUDE [Windows Push キャンペーンを作成する](../../includes/mobile-engagement-windows-push-campaign.md)]

アプリが実行中の場合は、アプリ内通知が表示されます。アプリが閉じている場合は、トースト通知が表示されます。トースト通知ではなくアプリ内通知が表示され、Visual Studio のデバッグ モードでアプリを実行している場合、ツールバーの **[ライフサイクル イベント] -> [中断]** を選択し、アプリが実際に中断していることを確認します。Visual Studio でアプリケーションをデバッグ中に [ホーム] ボタンをクリックしただけでは中断されず、アプリ内通知は表示されても、トースト通知は表示されないことがあります。

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows ストア デベロッパー センター]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png

<!---HONumber=AcomDC_0302_2016-->