<properties
	pageTitle="Windows ユニバーサル アプリの Azure Mobile Engagement の概要"
	description="Windows ユニバーサル アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/30/2015"
	ms.author="piyushjo"/>

# Windows ユニバーサル アプリの Azure Mobile Engagement の概要

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、アプリの使用状況を理解して、プッシュ通知を Windows ユニバーサル アプリケーションのセグメント化されたユーザーに送信するために、Azure Mobile Engagement の使用方法を説明します。このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。基本的なアプリ使用データを収集し、Windows 通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ユニバーサル アプリを作成します。このチュートリアルを完了すると、デバイスのプロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに、プッシュ通知をブロードキャストできるようになります。Mobile Engagement を使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。

このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [Mobile Engagement Windows ユニバーサル SDK]

> [AZURE.IMPORTANT]このチュートリアルを完了することは、Windows ユニバーサル アプリの他のすべての Mobile Engagement チュートリアルの前提条件です。このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>をご覧ください。

##<a id="setup-azme"></a>Windows ユニバーサル アプリの Mobile Engagement の設定

1. Azure ポータルにサインインし、画面の下部にある **[+新規]** をクリックします。

2. **[App Services]**、**[Mobile Engagement]**、**[作成]** の順にクリックします。

  	![][7]

3. 表示されたポップアップに、次の情報を入力します。

    ![][8]

	 - **アプリケーション名**: アプリケーションの名前を入力します。自由に任意の文字を使用してください。
	 - **プラットフォーム**: アプリ向けのターゲット プラットフォーム (**Windows ユニバーサル**) を選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。
	 - **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL の文字のみを使用する必要があります。自動生成された名前は、ベースとなる名前として役立ちます。また、この名前は一意である必要があるので、重複を避けるために、プラットフォーム名を付加することをお勧めします。
	 - **場所**: このアプリ (さらに重要なそのコレクション) がホストされるデータ センターを選択します。
	 - **コレクション**: アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は**新しいコレクション**を選択します。
	 - **コレクション名**。 これは、グループのアプリケーションを表します。すべてのアプリを 1 つのグループに含めることで、それらをメトリック集計できます。該当する場合は、ここで会社名や部門を使用する必要があります。

	> [AZURE.TIP]ユニバーサル アプリの対象プラットフォームを Windows と Windows Phone の両方にする場合、サポートされているプラットフォームごとに 1 つ、2 つの Mobile Engagement アプリケーションを作成する必要があります。そうすることで、対象ユーザーを適切にセグメント化し、プラットフォームに合わせた通知を送信できます。

4. **[アプリケーション]** タブで、作成したアプリを選択します。

5. **[接続情報]** をクリックして、モバイル アプリに統合する SDK に組み込む**接続**設定を表示します。

    ![][10]

6. **[接続文字列]** をコピーします - これは、アプリケーション コード内でこのアプリケーションを識別し、ユニバーサル アプリから Mobile Engagement サービスに接続するために必要なものです。

    ![][11]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。完全な統合のドキュメントについては、Mobile Engagement Windows ユニバーサル SDK ドキュメントをご覧ください。

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

###新しい Windows ユニバーサル アプリ プロジェクトを作成する

既にアプリが存在し、Windows ユニバーサル開発に精通している場合は、この手順を省略できます。

1. Visual Studio を起動し、**ホーム**画面で **[新しいプロジェクト]** を選択します。

2. ポップアップ ウィンドウで **[ストア アプリ]** -> **[ユニバーサル アプリ]** -> **[空のアプリケーション (ユニバーサル アプリ)]** の順に選択します。アプリの **[名前]** と **[ソリューション名]** に入力し、**[OK]** をクリックします。

    ![][13]

これで、Azure Mobile Engagement SDK を統合する新しい Windows ユニバーサル アプリ プロジェクトが作成されました。

###アプリを Mobile Engagement のバックエンドに接続する

1. プロジェクトに[Mobile Engagement Windows ユニバーサル SDK] nuget パッケージをインストールします。Windows と Windows Phone の両方のプラットフォームを対象としている場合、両方のプロジェクトにこれを行う必要があります。同じ Nuget パッケージが各プロジェクトに適切なプラットフォーム固有のバイナリを配置します。

2. **Package.appxmanifest** を開き、次が自動的に追加されない場合、それを追加します。

		Internet (Client)

	![][20]

3. 先にコピーした Mobile Engagement アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間にコピーします。

	![][22]

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

##<a id="monitor"></a>リアルタイム監視を有効にする

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。そのために、Mobile Engagement SDK が提供する **EngagementPage** を使用して、**MainPage** をサブクラス化します。

1. 	`using` ステートメントを追加します。

		   using Microsoft.Azure.Engagement;

2. **EngagementPage** を使用して、**Page** の前にある **MainPage** のスーパー クラスを置き換えます。

	![][23]

3. `MainPage.xml` ファイルで、次の操作を行います。

	a.次の内容を名前空間宣言に追加します。

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b.XML タグ名の **Page** を **engagement:EngagementPage** で置換します。
	
> [AZURE.IMPORTANT]ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。これは既定のテンプレートに `OnNavigatedTo` メソッドがある Windows Phone プロジェクトで特に重要です。

###アプリがリアルタイム監視に接続していることを確認する

このセクションでは、Mobile Engagement のリアルタイム監視機能を使用して、アプリが Mobile Engagement のバックエンドに接続していることを確認する方法について説明します。

1. Mobile Engagement ポータルに移動します。

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の **[関与]** ボタンをクリックします。

	![][26]

2. アプリの Engagement ポータルの **[設定]** ページに移動します。ここで、以下に示す **[モニター]** タブをクリックします。![][30]

3. モニターにはアプリを起動するデバイスがリアルタイムで表示されます。

4. Visual Studio に戻り、エミュレーターまたは接続されたデバイスのいずれかでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションがリアルタイム表示されます。![][33]

**ご利用ありがとうございます。** これで終了です。このチュートリアルの最初の手順が正常に完了し、アプリが Mobile Engagement のバックエンドに接続され、データが送信されました。

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーに情報を提供できます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

1. `Package.appxmanifest` ファイルの **[アプリケーション]** タブの **[通知]** で **[トースト対応:]** に **[はい]** を選択します。

	![][35]

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

2. Mobile Engagement ポータルの **[設定]** に移動し、左側の **[ネイティブ プッシュ]** セクションをクリックします。

3. **[編集]** ボタンをクリックし、以下に示すように、**パッケージ セキュリティ ID (SID)** と**シークレット キー**を入力します。

	![][36]

##<a id="send"></a>アプリへ通知を送信する

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. Mobile Engagement ポータルで [**リーチ**] タブに移動します。

2. **[新しいお知らせ]** をクリックし、プッシュ通知キャンペーンを作成します。![][37]

3. 次の手順に従い、キャンペーンの最初のフィールドをセットアップします。![][38]

	a.キャンペーンの名前を付けます。

	b.**[配信時刻]** で *[指定なし]* を選択し、アプリが起動されているかどうかに関係なく、アプリが通知を受信できるようにします。

	c.通知テキストに、プッシュ通知内で太字で表示される**タイトル**を入力します。

	d.次に、メッセージを入力します。

4. 下にスクロールし、**[コンテンツ]** セクションで **[通知のみ]** を選択します。![][39]

5. 最も基本的なキャンペーンの設定が完了しました。もう一度下にスクロールし、**[作成]** ボタンをクリックしてキャンペーンを保存します。

6. 最後の手順として、**[アクティブ化]** をクリックしてキャンペーンをアクティブにし、プッシュ通知を送信します。![][41]

デバイスにキャンペーンのトースト通知が表示されるはずです。このトースト通知を表示するには、アプリを閉じる必要があります。アプリが実行中の場合、キャンペーンをアクティブにしてトースト通知を受信できるようにする数分前にアプリを閉じます。通知がアプリに表示されるように、アプリ内通知を統合する場合、「[Windows ユニバーサル アプリ - オーバーレイ統合]」を参照してください。

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows ユニバーサル SDK]: http://go.microsoft.com/?linkid=9864592
[Windows ストア デベロッパー センター]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows ユニバーサル アプリ - オーバーレイ統合]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->