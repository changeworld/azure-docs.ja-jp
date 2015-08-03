<properties 
	pageTitle="Windows ユニバーサル アプリ Reach SDK 統合" 
	description="Windows ユニバーサル アプリと Azure Mobile Engagement Reach を統合する方法"
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
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Windows ユニバーサル アプリ Reach SDK 統合

このガイドの手順を実行する前に、[Windows Universal Engagement SDK 統合](mobile-engagement-windows-store-integrate-engagement.md)の統合手順を実行する必要があります。

##エンゲージメント Reach SDK を Windows ユニバーサル プロジェクトに組み込む

追加するものは何もありません。`EngagementReach` のリファレンスとリソースは、既にプロジェクト内に存在します。

> [AZURE.TIP]プロジェクトの `Resources` フォルダーにあるイメージをカスタマイズできます (特に、既定の Engagement アイコンであるブランド アイコン)。ユニバーサル アプリでは、共有プロジェクトの `Resources` フォルダーを移動させ、そのコンテンツをアプリ間で共有できますが、`Resources\EngagementConfiguration.xml` ファイルはプラットフォーム依存のため、その既定の場所に維持する必要があります。

##Windows 通知サービスを有効にする

`Package.appxmanifest` ファイルで **Windows 通知サービス** (WNS) を使用するために、`Application UI` で左下のボックスから [`All Image Assets`] をクリックします。`Notifications` のボックスの右で、`toast capable` を `(not set)` から `(Yes)` に変更します。

さらに、アプリを、Microsoft アカウントとエンゲージメント プラットフォームと同期させる必要があります。これには、アカウントを作成するか、[Windows デベロッパー センター](https://dev.windows.com)にログインする必要があります。その後に、新しいアプリケーションを作成して SID と秘密キーを検索します。エンゲージメントのフロントエンドで、[`native push`] のアプリ設定に移動し、資格情報を貼り付けます。その後、プロジェクトを右クリックし、[`store`] と [`Associate App with the Store...`] を選択します。同期するには、前に作成したアプリケーションを選択する必要があります。

##Engagement Reach SDK を初期化する

`App.xaml.cs` を変更します。

-   次の内容を `using` ステートメントに追加します。

		using Microsoft.Azure.Engagement;

-   `OnLaunched` の `EngagementAgent.Instance.Init` の直後に `EngagementReach.Instance.Init` を挿入します。

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

-   コマンド、別のアプリケーション、またはカスタム スキームによってアプリがアクティブ化されたときに、Engagement Reach を有効にするには、`OnActivated` メソッドを次のようにオーバーライドします。

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

	`EngagementReach.Instance.Init` は専用のスレッドで稼働します。自分で実行する必要はありません。

> [AZURE.TIP]プロジェクトの `Resources\EngagementConfiguration.xml` ファイルの `<channelName></channelName>` で、アプリの WNS プッシュ チャネルの名前を指定できます。既定では、Engagement はアプリ ID に基づいて、名前を作成します。Engagement の外部でプッシュ チャネルを使用する計画がある場合を除いて、自分で名前を指定する必要はありません。

##統合

Engagement では、2 通りの方法で Reach の通知とアナウンスを実装できます。オーバーレイ統合と Web ビュー統合です。

オーバーレイ統合の場合、アプリケーションに大量のコードを記述する必要がありません。EngagementPageOverlay を使用して、ページ、xaml ファイル、cs ファイルにタグ付けをするだけです。さらに、Engagement の既定のビューをカスタマイズした場合、そのカスタマイズはタグ付けされたすべてのページで共有されるので、一度定義するだけで済みます。ページが EngagementPageOverlay 以外のオブジェクトから継承する必要がある場合は、処理が中断し、Web ビュー統合を使用するように強制されます。

Web ビュー統合の場合、実装方法がより複雑です。ただし、アプリ ページが「Page」以外のオブジェクトから継承する必要がある場合は、Web ビューとその動作を統合する必要があります。

> [AZURE.TIP]ルート レベルの `<Grid></Grid>` 要素を追加し、ページの全内容を囲むことを検討してください。Web ビュー統合では、Web ビューをこのグリッドの子として追加します。Engagement コンポーネントを他の場所に設定する必要がある場合は、表示サイズを自分で管理する必要があることに注意してください。

### オーバーレイ統合

Engagement は、通知とアナウンスを表示するためのオーバーレイを提供します。

オーバーレイ統合を使用する場合は、Web ビュー統合を使用しないでください。

.xaml ファイルで、EngagementPage リファレンスを EngagementPageOverlay に変更します。

-   次の内容を名前空間宣言に追加します。

			xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   `engagement:EngagementPage` を `engagement:EngagementPageOverlay` に置き換えます。

**EngagementPage を使用する場合:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**EngagementPageOverlay を使用する場合:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **EngagementPageOverlay for 8.1 を使用する場合:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

次に .cs ファイルで、"EngagementPage" の代わりに "EngagementPageOverlay" でページをタグ付けし、"Microsoft.Azure.Engagement.Overlay" をインポートします。

			using Microsoft.Azure.Engagement.Overlay;

-   `EngagementPage` を `EngagementPageOverlay` に置き換えます。

**EngagementPage を使用する場合:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**EngagementPageOverlay を使用する場合:**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

このページがエンゲージメント オーバーレイ メカニズムを使用するようになったので、Web ビューを挿入する必要はありません。

エンゲージメント オーバーレイは、xaml ファイルで見つけた最初の「Grid」要素を使用し、ページに 2 つの Web ビューを追加します。Web ビューが設定される位置を決めるために、「EngagementGrid」という名前のグリッドを次のように定義ができます。

			<Grid x:Name="EngagementGrid"></Grid>

通知とアナウンスのオーバーレイは、xaml ファイルと cs ファイルで直接カスタマイズできます。

-   `EngagementAnnouncement.html` : `Announcement` Web ビュー html デザイン。
-   `EngagementOverlayAnnouncement.xaml` : `Announcement` xaml デザイン。
-   `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` リンク コード。
-   `EngagementNotification.html` : `Notification` Web ビュー html デザイン。
-   `EngagementOverlayNotification.xaml` : `Notification` xaml デザイン。
-   `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` リンク コード。
-   `EngagementPageOverlay.cs` : `Overlay` アナウンスと通知の表示コード。

### Web ビュー統合

Web ビュー統合を使用する場合は、オーバーレイ統合を使用しないでください。

エンゲージメントの内容を表示するには、各ページで 2 つの xaml Web ビューを統合し、通知とアナウンスを表示する必要があります。そのため、次のコードを xaml ファイルに追加します。

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **8.1 統合の場合:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

関連する .cs ファイルは、次の内容である必要があります。

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> Web ビューが組み込まれている実装では、デバイス画面の向きが変わると、サイズが変わります。

##データのプッシュを操作する (オプション)

アプリケーションが Reach データのプッシュを受信できるようにするには、EngagementReach クラスの 2 つのイベントを実装する必要があります。

"Public App(){}" の App.xaml.cs で次のように追加します。

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			  Debug.WriteLine("String data push message received: " + body);
			  return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			  Debug.WriteLine("Base64 data push message received: " + encodedBody);
			  // Do something useful with decodedBody like updating an image view
			  return true;
			};

各メソッドのコールバックがブール値を返すことを確認できます。データのプッシュをディスパッチした後、Engagement はバックエンドにフィードバックを送信します。コールバックが false を返した場合、`exit` フィードバックが送信されます。それ以外の場合は、`action` になります。イベントにコールバックが設定されていない場合は、`drop` フィードバックが Engagement に返されます。

> [AZURE.WARNING]Engagement は、データのプッシュのフィードバックを複数受信することができません。複数のハンドラーをイベントに設定する計画がある場合は、最後に送信されたものがフィードバックに相当することに注意してください。この場合、フロントエンドでフィードバックが混同されるのを避けるために、常に同じ値を返すことをお勧めします。

##UI をカスタマイズする (オプション)

### 最初の手順

Reach UI をカスタマイズできるようにします。

そのためには、`EngagementReachHandler` クラスのサブクラスを作成する必要があります。

**サンプル コード:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  internal class ExampleReachHandler : EngagementReachHandler
			  {
			   // Override EngagementReachHandler methods depending on your needs
			  }
			}

次に、`App()` メソッドに含まれる `App.xaml.cs` クラスのカスタム オブジェクトを使用して、`EngagementReach.Instance.Handler` フィールドの内容を設定します。

**サンプル コード:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE]既定では、Engagement は `EngagementReachHandler` の独自の実装を使用します。自分用の実装を作成する必要はなく、そうする場合でも、すべてのメソッドをオーバーライドする必要はありません。既定の動作では、Engagement の基本オブジェクトを選択します。

### Web ビュー

既定では、Reach は DLL に組み込まれたリソースを使用して、通知とページを表示します。

完全なカスタマイズを可能にするには、Web ビューのみを使用します。レイアウトをカスタマイズする場合は、リソース ファイルの `EngagementAnnouncement.html` と `EngagementNotification.html` を直接上書きしてください。Engagement を正常に実行するには、すべてのコードを `<body></body>` の中に含める必要があります。ただし、タグを `engagement_webview_area` の外部に追加ができます。

また、独自のリソースを使用することも決定できます。

サブクラスの `EngagementReachHandler` メソッドをオーバーライドして、独自のレイアウトを使用するように Engagement に指示ができますが、組み込まれたエンゲージメントのメカニズムに注意してください。

**サンプル コード:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


既定では、AnnouncementHTML は `ms-appx-web:///Resources/EngagementAnnouncement.html` です。これは、プッシュ メッセージの内容を設計する html ファイルを示します (Text アナウンス、Web アナウンス、Poll アナウンス)。AnnouncementName は `engagement_announcement_content` です。これは、xaml ページの Web ビュー設計の名前です。

NotfificationHTML は `ms-appx-web:///Resources/EngagementNotification.html` です。これは、プッシュ メッセージの通知を設計する html ファイルを示します。NotfificationName は `engagement_notification_content` です。これは、xaml ページの Web ビュー設計の名前です。

### カスタマイズ

Engagement オブジェクトを保持する場合、希望する通知とアナウンスの Web ビューをカスタマイズできます。Web ビュー オブジェクトは 3 回記述されていることに注意してください。1 回目は xaml ファイルで、2 回目は .cs ファイルの "setwebview()" メソッドで、3 回目は html ファイルで記述されます。

-   xaml ファイルでは、現在のグラフィカル レイアウトの Web ビュー コンポーネントを記述します。
-   .cs ファイルでは、"setwebview()" を定義して、2 つの Web ビュー (通知とアナウンス) のディメンションを設定できます。この機能は、アプリケーションがサイズ変更する場合に非常に効果的です。
-   Engagement の html ファイルで、Web ビューのコンテンツ、デザイン、互いの要素間の位置を記述します。

### メッセージを起動する

ユーザーがシステム通知 (トースト) をクリックすると、Engagement がアプリケーションを起動して、プッシュ メッセージの内容をロードし、対応するキャンペーンのページを表示します。

アプリケーションが起動されてからページが表示されるまでの間に、(ネットワークの速度に応じて) 遅延が発生します。

読み込み中であることをユーザーに示すには、進行状況バーや進行状況インジケーターなどの視覚的な情報を提供する必要があります。Engagement 自身では、それに対応することはできませんが、いくつかのハンドラーを提供します。

コールバックを実装するには、App.xaml.cs の "Public App(){}" に次の内容を追加します。

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

できれば、`EngagementReach.Instance.Init()` を呼び出す前に、`App.xaml.cs` ファイルの "Public App(){}" メソッドにコールバックを設定します。

> [AZURE.TIP]各ハンドラーは、UI スレッドにより呼び出されます。メッセージボックスや UI 関連のものは、安心して使用できます。

##カスタム スキーマのヒント

カスタム スキーマを使用すると、エンゲージメントのフロントエンドから、エンゲージメント アプリケーションで使用する別の種類の URI を送信できます。`http, ftp, ...` などの既定のスキーマは Windows により管理されます。デバイスに既定のアプリケーションがインストールされていない場合は、プロンプト ウィンドウが表示されます。アプリケーションのスキーマなど、他のスキーマも使用できます。さらに、アプリケーションに対してカスタム スキーマを使用することもできます。

カスタム スキーマをアプリケーションで設定する簡単な方法は、`Package.appxmanifest` を開いて、[`Declarations`] パネルに移動することです。[使用可能な宣言] スクロール ボックスで、[`Protocol`] を選択して追加します。新しいプロトコルの希望する名前を使用して、[`Name`] フィールドを編集します。

これらのプロトコルを使用するには、`OnActivated` メソッドを使用して `App.xaml.cs` を編集します。ここで、エンゲージメントの初期化も忘れないでください。

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion
 

<!---HONumber=July15_HO4-->