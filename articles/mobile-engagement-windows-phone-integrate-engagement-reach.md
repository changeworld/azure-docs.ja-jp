<properties 
	pageTitle="Azure モバイル エンゲージメント Windows Phone SDK 統合" 
	description="Windows Phone にエンゲージメント リーチを統合する方法"				
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Windows Phone プロジェクトにエンゲージメント リーチ SDK を統合する方法

このガイドに従う前に [Windows Phone にエンゲージメントを統合する方法]ドキュメントで説明されている(../mobile-engagement-windows-phone-integrate-engagement/) 統合手順を実行する必要があります。

##エンゲージメント リーチ SDK を Windows Phone プロジェクトに埋め込む

追加するものはありません。 `EngagementReach` 参照とリソースは既にプロジェクトに含まれています。

> [AZURE.TIP]  プロジェクトの  `Resources` フォルダーにあるイメージ、特にブランド アイコン（既定のエンゲージメントアイコン）をカスタマイズができます。

##機能を追加する

エンゲージメント リーチ SDK には追加の機能が必要です。

 `WMAppManifest.xml` ファイルを開き、次の機能が  `Capabilities` パネルで宣言されていることを必ずご確認ください:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

##Microsoft プッシュ通知サービスを有効にする

**Microsoft プッシュ通知サービス** (MPNS と呼ばれます) を使用するには、 `WMAppManifest.xml` ファイルに、プロジェクトの名前に `Publisher` 属性が設定された `<App />`  タグが必要です。

##エンゲージメント リーチ SDK の初期化

### エンゲージメントの構成

エンゲージメントの構成は、プロジェクトの  `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、リーチの構成を指定します:

-   *オプション*、ネイティブのプッシュ (MPNS) が、`<enableNativePush>` タグと`</enableNativePush>` タグの間でアクティブになっているかどうかを示します (既定は `true`)。
-   *オプション*、`<channelName>` タグと `</channelName>` タグの間のプッシュ チャネルの名前を示します。アプリケーションが現在使用しているものの名前を指定するか、または空のままにします。

代わりに指定を実行時に行う場合は、エンゲージメント エージェントを初期化する前に、次のメソッドを呼び出すことができます。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			engagementConfiguration.Reach.EnableNativePush = true;                  /* [Optional] whether the native push (MPNS) is activated or not. */
			engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   /* [Optional] Provide the same channel name that your application may currently use. */
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] アプリケーションの MPNS プッシュ チャネルの名前を指定できます。既定では、エンゲージメントは appId に基づく名前を作成します。プッシュ チャネルをエンゲージメント以外で使用する予定がある場合を除き、自分で名前を指定する必要はありません。

### エンゲージメントの初期化

 `App.xaml.cs` の変更:

-    `使用している` ステートメントに追加 :

			using Microsoft.Azure.Engagement;

-    `EngagementReach.Instance.Init` を  `Application_Launching` の  `EngagementAgent.Instance.Init` の直後に挿入します :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   EngagementAgent.Instance.Init();
			   EngagementReach.Instance.Init();
			}

-    `EngagementReach.Instance.OnActivated` を  `Application_Activated` メソッドに挿入します :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			   EngagementReach.Instance.OnActivated(e);
			}

> [AZURE.IMPORTANT] 専用のスレッドで  `EngagementReach.Instance.Init` が実行されます。自分で実行する必要はありません。

送信
----------

Microsoft はプッシュ通知を使用する場合のルールを設定しています。

Microsoft [アプリケーション ポリシー] ドキュメント、セクション 2.9:

> 2.9.1) アプリケーションは、最初に提供される通知について説明し、**ユーザーの明確な許可 (オプトイン)**を取得し、また**ユーザーがプッシュ通知をオプトアウトできるメカニズムを提供する必要があります**。Microsoft プッシュ通知サービスを使用して提供されるすべての通知は、ユーザーに提供される説明に従っている必要があり、適用可能なすべての [アプリケーション ポリシー]、[コンテンツ ポリシー]、[特定のアプリケーションの種類の追加要件] に準拠している必要があります。

**ショート**:ユーザーにプッシュ通知を受信することに同意するように依頼する必要があります。次に、設定で、プッシュ通知を無効にする方法を追加します。

EngagementReach オブジェクトは、オプトイン / オプトアウト、 `EnableNativePush()` と  `DisableNativePush()` を管理する 2 つの方法を提供します。たとえば、設定でMPNS を有効または無効に切り替えるオプションを作成ができます。

エンゲージメントの設定で MPNS を非アクティブ化することもできます \<windows-phone-sdk-reach-configuration\>。

> 2.9.2) アプリケーションと Microsoft プッシュ通知サービスのアプリケーションの使用量は、Microsoft が合理的な裁量によって決定した、Microsoft プッシュ通知サービスのネットワーク容量または帯域幅を超えてはいけません。さもないと、過剰なプッシュ通知により、Windows Phone または、その他の Microsoft デバイスまたはサービスに過剰に負担がかかります。また Microsoft のネットワークまたはサーバーまたは Microsoft プッシュ通知サービスに接続されるサード パーティ製のサーバーまたはネットワークに損害を与えたり、干渉してはいけません。

**ショート**:プッシュ通知を使用しすぎないようにします。これについてはエンゲージメントが対応します。

> 2.9.3) Microsoft プッシュ通知サービスは、ミッション クリティカルな通知、それ以外の生死に関わる問題に影響を与える通知、医療機器または条件に関連するクリティカルな通知を含みますが、これらに限定することなく、これらの送信には使用できません。MICROSOFT は、Microsoft プッシュ通知サービスの使用またはMicrosoft プッシュ通知サービスの通知の配信が、中断やエラーがなく実行されること、また、リアルタイムで配信されることについての保証を一切いたしません。

**ショート**:重要な情報の送信では MPNS に依存しないでください。エンゲージメントは MPNS を使用するため, このルールはエンゲージメントのフロントエンド内で作成されるキャンペーンにも適用されます。

**これらの推奨事項を順守しない場合は、アプリケーションが検証プロセスに合格することを保証できません。**

##datapush の処理 (オプション)

アプリケーションでリーチ データ プッシュを受信する場合は、EngagementReach クラスの 2 つのイベントを実装する必要があります:

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

各メソッドのコールバックがブール値を返すことを確認できます。データ プッシュのディスパッチ後に、エンゲージメントはそのバックエンドにフィードバックを送信します。コールバックが、false を返した場合、 `exit` フィードバックが送信されます。それ以外の場合は、 `action` になります。イベントにコールバックが設定されていない場合は、 `drop` フィードバックがエンゲージメントに返されます。

> [AZURE.WARNING] エンゲージメントはデータ プッシュに対して複数のフィードバックを受信することはできません。1 つのイベントに複数のハンドラーを設定する場合は、フィードバックは最後に送信されたハンドラーに対応することに注意してください。この場合、フロントエンドでフィードバックが混乱するのを避けるために、常に同じ値を返すことを推奨します。

##UI のカスタマイズ (オプション)

### 最初の手順

リーチ UI をカスタマイズができます。

これを実行するには、 `EngagementReachHandler` クラスのサブクラスを作成する必要があります。

**サンプル コード :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			   internal class ExampleReachHandler : EngagementReachHandler
			   {
			      // Override EngagementReachHandler methods depending on your needs
			   }
			}

次に、 `Application_Launching` メソッド内の  `App.xaml.cs` クラスのカスタム オブジェクトの  `EngagementReach.Instance.Handler` フィールドの内容を設定します。

**サンプル コード :**

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   // your app initialization 
			   EngagementReach.Instance.Handler = new ExampleReachHandler();
			   // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] 既定では、エンゲージメントは、 `EngagementReachHandler` の独自の実装を使用します。独自に作成する必要はありません。作成した場合は、すべてのメソッドをオーバーライドする必要はありません。既定の動作では、エンゲージメント ベース オブジェクトを選択します。

### レイアウト

既定では、リーチは DLL の埋め込みリソースを使用して、通知とページを表示します。

ただし、これらのコンポーネントにブランドを反映するように独自のリソースを使用ができます。

サブクラスに  `EngagementReachHandler` メソッドをオーバーライドして、エンゲージメントにレイアウトを使用させることができます:

**サンプル コード :**

			// In your subclass of EngagementReachHandler
			
			public override string GetTextViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetWebViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetPollUri()
			{
			   // return the path of your own xaml
			}
			
			public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
			{
			   // return a new instance of your own notification
			}

> [AZURE.TIP]  `CreateNotification` メソッドは null を返すことができます。通知は表示されず、リーチ キャンペーンは削除されます。

レイアウトの実装を簡素化するために、コードの基礎として機能する独自の xaml を提供します。それらはエンゲージメント SDK アーカイブに配置されています (/src/reach/)。

> [AZURE.WARNING] 提供するソースは、使用されているものと全く同じものです。それゆえ、直接変更する場合は、名前空間と名前を忘れずに変更します。

### 通知の位置

既定では、アプリ内通知はアプリケーションの下部左側に表示されます。 `EngagementReachHandler` オブジェクトの  `GetNotificationPosition` メソッドをオーバーライドして、この動作を変更できます。

			// In your subclass of EngagementReachHandler
			
			public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
			{
			   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
			}

現在、 `BOTTOM` (既定) と  `TOP` の間で選択できます。

### メッセージの起動

ユーザーがシステム通知 (トースト) をクリックすると、エンゲージメントはアプリを起動し、プッシュ メッセージの内容を読み込んで、対応するキャンペーンのページを表示します。

アプリケーションが起動されてからページが表示されるまでには、遅延が発生します (ネットワークの速度によります)。

読み込み中であることをユーザーに示すには、進行状況バーや進行状況インジケータエーなどの視覚的な情報を提供する必要があります。エンゲージメント自身ではそれに対応することはできませんが、いくつかのハンドラーを提供します。

コールバックを実装するには次のことを実行します:

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

 `EngagementReach.Instance.Init()` コールの前に、 `App.xaml.cs` ファイルの  `Application_Launching` メソッドにコールバックを設定できます。

> [AZURE.TIP] 各ハンドラーは UI スレッドに呼び出されます。メッセージ ボックス、または UI に関連するものを使用する場合は考慮する必要はありません。


[アプリケーション ポリシー]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[コンテンツ ポリシー]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[特定のアプリケーションの種類の追加要件]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

<!--HONumber=47-->
