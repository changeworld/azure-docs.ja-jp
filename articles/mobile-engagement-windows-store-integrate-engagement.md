<properties 
	pageTitle="Azure モバイル エンゲージメント Windows ストア SDK の概要" 
	description="Azure モバイル エンゲージメントの Windows ストア SDK の最新更新プログラムと手順について" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Windows でエンゲージメントを統合する方法

この手順では、Windows アプリケーションでエンゲージメントの分析機能と監視機能をアクティブ化する最も簡単な方法を説明します。

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。イベント、エラー、ジョブなどの他の統計情報を計算するのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、エンゲージメント API を使用して手動で実行する必要があります ( [Windows ストア アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法](../mobile-engagement-windows-store-use-engagement-api/)を参照)。.

##サポートされているバージョン

この Windows SDK は、次のアプリケーションのみをサポートします。

-   Windows 8 C\# ストア アプリケーション
-   Windows 8.1 C\# ストア アプリケーション

##エンゲージメント SDK を Windows プロジェクトに組み込む

Microsoft 開発プラットフォーム向けの NuGet パッケージ マネージャーを使用して、エンゲージメント SDK を組み込みます。

モバイル エンゲージメント向け Nuget パッケージは、まだオンラインで使用できないので、SDK アーカイブをダウンロードして解凍する必要があります。次に、Visual Studio でプロジェクトを右クリックし、[Nuget パッケージの管理]、[設定] の順に移動して、lib フォルダーを新しいパッケージ ソースとして追加します。

> [AZURE.NOTE] NuGet は、.net プロジェクト用の主要なパッケージ マネージャーです。エンゲージメント SDK を使用するために必要なすべてのものを、ダウンロード、追加、コピーします。エンゲージメントの Windows 8 C\# Metro アプリケーションは、クロスプラットフォームの移植性を実現するために、サード パーティのライブラリ (Microsoft.Bcl.Build v1.0.10 と Microsoft.Bcl portability pack v1.1.3) を使用するので、NuGet は自動的にそれらをインストールするように求めてきます。詳細については、[NuGet の Web サイト]をご覧ください。

##機能を追加する

エンゲージメント SDK が適切に機能するには、Windows SDK の機能が一部必要になります。

`Package.appxmanifest` ファイルを開き、次の機能が宣言されていることを確認します。

-   `Internet (Client)`

Package.appxmanifest ファイルの `Declarations` パネルに移動します。

`[使用可能な宣言]` で、`[ファイルの種類の関連付け]`  を選択して追加します。右側の画面の `[名前]` フィールドに、`engagement_log_file` を入力し、[ファイルの種類] フィールドに `.set` を入力します。

`[使用可能な宣言]` で、`[キャッシュ ファイル更新プログラム]`  を選択して追加します。

##エンゲージメント SDK を初期化する

### エンゲージメントの構成

エンゲージメントの構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、次の内容を指定します。

-   `<connectionString>` タグと `<\connectionString>` タグの間にアプリケーションの接続文字列

代わりに指定を実行時に行う場合は、エンゲージメント エージェントを初期化する前に、次のメソッドを呼び出すことができます。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

### エンゲージメントの初期化

新しいプロジェクトを作成すると、`App.xaml.cs` ファイルが生成されます。このクラスは`アプリケーション`から継承され、多数の重要なメソッドを含んでいます。また、エンゲージメント SDK を初期化するためにも使用されます。

`App.xaml.cs`: を変更します。

-   次の内容を `using` ステートメントに追加します。

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   カスタム スキーマ、別のアプリケーション、コマンドラインを使用してアプリケーションが起動されると、`OnActivated` メソッドが呼び出されます。また、アプリをアクティブ化する際は、エンゲージメント エージェントを初期化する必要があります。そのために、`OnActivated` メソッドをオーバーライドします。

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] エンゲージメントの初期化は、アプリケーションの別の場所には追加しないことを強くお勧めします。

##基本的なレポート

### 推奨する方法: `Page` クラスをオーバーロードする

ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、エンゲージメントが必要とするすべてのログのレポートを有効にするには、単純にすべての `Page`  サブクラスが `EngagementPage`  クラスから継承されるようにします。

次に、この操作をアプリケーションのページに対して実行する例を示します。同じことを、アプリケーションのすべてのページに対して実行できます。

#### C\# ソース ファイル

ページの `.xaml.cs` ファイルを変更します。

-   次の内容を `using` ステートメントに追加します。

			using Microsoft.Azure.Engagement;

-   `Page` を `EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**エンゲージメントを使用する場合:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内の `StartActivity` を呼び出します)。

#### XAML ファイル

ページの `.xaml` ファイルを変更します。

-   次の内容を名前空間宣言に追加します。

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   `Page` を `engagement:EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**エンゲージメントを使用する場合:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
			</engagement:EngagementPage >

#### 既定の動作をオーバーライドする

既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。クラスが「Page」サフィックスを使用している場合、エンゲージメントはそれも削除します。

名前の既定の動作をオーバーライドする場合は、単純に次の内容をコードに追加します。

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			  /* your code */
			  return "new name";
			}

アクティビティに関する追加情報を報告する場合は、次の内容をコードに追加します。

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			  /* your code */
			  return extra;
			}

追加情報に関する詳細は、\<windows-sdk-extras-parameters\> をご覧ください。

これらのメソッドは、ページの  `OnNavigatedTo` メソッドから呼び出されます。

### 代替の方法: 手動で  `StartActivity()` を呼び出す

 `Page` クラスをオーバーロードできない、またはしない場合は、代わりに  `EngagementAgent` メソッドを直接呼び出して、アクティビティを開始できます。

ページの  `OnNavigatedTo` メソッドの中から  `StartActivity` を呼び出すことをお勧めします。

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Windows SDK は、アプリケーションを終了する際に、自動的に  `EndActivity` メソッドを呼び出します。したがって、 `EndActivity` メソッドを呼び出すと現在のセッションが強制的に終了されるので、このメソッドを呼び出さないようにし、ユーザーのアクティビティが変わる際には常に  `StartActivity` メソッドを呼び出すことを強くお勧めします。 *HIGHLY* *NEVER*

##詳細な報告

オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、 `EngagementAgent` クラスの別のメソッドを使用します。エンゲージメント API により、エンゲージメントのすべての高度な機能を使用できます。

詳細については、「Windows でエンゲージメント API を使用する方法」をご覧ください。

##詳細な構成

### 自動クラッシュ レポートを無効にする

エンゲージメントの自動クラッシュ レポート機能を無効にできます。その場合、未処理の例外が発生しても、エンゲージメントは何も行いません。

> [AZURE.WARNING] この機能を無効にする計画がある場合は、アプリで未処理の例外が発生しても、エンゲージメントはクラッシュを報告せず、**さらに**セッションとジョブを終了しないことに注意してください。

自動クラッシュ レポートを無効にするには、宣言した方法に応じて、構成をカスタマイズするだけです。

####  `EngagementConfiguration.xml` ファイルを使用する場合

`<reportCrash>`  タグと `</reportCrash>` タグの間で、report crash を  `false` に設定します。

#### 実行時に  `EngagementConfiguration` オブジェクトを使用する場合

EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### バースト モード [ベータ版]

既定では、エンゲージメント サービスはログをリアルタイムで報告します。アプリケーションがログを非常に頻繁に報告する場合、ログをバッファーに格納して、それらすべてを定期的に一度に報告する方が適切です (これを「バースト モード」と呼びます)。

そのためには、次のメソッドを呼び出します。

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は、**ミリ秒**単位です。リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、エンゲージメントの監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。

> [AZURE.WARNING] また、バーストのしきい値は、1 秒よりも短い期間に設定することはできません。それを試みた場合、SDK はエラーのトレースを表示し、自動的に既定値 (0 秒) に再設定します。これにより、SDK はログをリアルタイムで報告するようになります。

[ここからダウンロードします]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet の Web サイト]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
