<properties 
	pageTitle="Windows ユニバーサル アプリ Engagement SDK 統合" 
	description="Windows ユニバーサル アプリと Azure Mobile Engagement を統合する方法" 					
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

#Windows ユニバーサル アプリ Engagement SDK 統合

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

この手順では、Windows ユニバーサル アプリケーションで Engagement の分析機能と監視機能をアクティブ化する最も簡単な方法を説明します。

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。イベント、エラー、ジョブなどの他の統計情報を計算するのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、Engagement API を使用して手動で実行する必要があります ([Windows ユニバーサル アプリで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-windows-store-use-engagement-api.md)を参照)。

##サポートされているバージョン

Mobile Engagement SDK for Windows ユニバーサル アプリは次を対象とした Windows ランタイム アプリケーションにのみ統合できます。

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1

> [AZURE.NOTE]Windows Phone 8.1 Silverlight を対象としている場合は、[Windows Phone Silverlight の統合手順](mobile-engagement-windows-phone-integrate-engagement.md)を参照してください。


##Mobile Engagement ユニバーサル アプリ SDK をインストールします。

Mobile Engagement SDK for Windows Universal App は「*MicrosoftAzure.MobileEngagement*」という名称の Nuget パッケージとして利用できます。これは Visual Studio Nuget Package Manager からインストールできます。

##機能を追加する

Engagement SDK が適切に機能するには、Windows SDK の機能が一部必要になります。

`Package.appxmanifest` ファイルを開き、次の機能が宣言されていることを確認します。

-   `Internet (Client)`

##Engagement SDK を初期化する

### Engagement の構成

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、次の内容を指定します。

-   `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

        #if WINDOWS_PHONE_APP
          /* Connection string for my Windows Phone App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        #else
          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        #endif

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

> [AZURE.WARNING]条件付きコンパイル シンボル `WINDOWS_PHONE_APP` を使用し、スタンドアロンの Windows ランタイム アプリで別の構成を定義する必要はありません。プラットフォームが 1 つだけだからです。

### Engagement の初期化

新しいプロジェクトを作成すると、`App.xaml.cs` ファイルが生成されます。このクラスは、`Application` から継承し、多くの重要なメソッドが含まれています。また、Engagement SDK を初期化するためにも使用されます。

`App.xaml.cs` を変更します。

-   次の内容を `using` ステートメントに追加します。

		using Microsoft.Azure.Engagement;

-   `EngagementAgent.Instance.Init` を `OnLaunched` メソッドに挿入します :

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		
		  // or
		
		  EngagementAgent.Instance.Init(args, engagementConfiguration);
		}

-   カスタム スキーマ、別のアプリケーション、コマンドラインを使用してアプリケーションが起動されると、`OnActivated` メソッドが呼び出されます。また、アプリをアクティブ化する際は、 Engagement エージェントを初期化する必要があります。そのために、`OnActivated` メソッドをオーバーライドします。

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		
		  // or
		
		  EngagementAgent.Instance.Init(args, engagementConfiguration);
		}

> [AZURE.IMPORTANT] Engagement の初期化は、アプリケーションの別の場所には追加しないことを強くお勧めします。

##基本的なレポート

### 推奨される方法: `Page` クラスをオーバーロードします

ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、エンゲージメントが必要とするすべてのログのレポートを有効にするには、単純にすべての `Page` サブクラスが `EngagementPage` クラスから継承されるようにします。

次に、この操作をアプリケーションのページに対して実行する例を示します。同じことを、アプリケーションのすべてのページに対して実行できます。

#### C# ソース ファイル

ページの `.xaml.cs` ファイルを変更します。

-   次の内容を `using` ステートメントに追加します。

		using Microsoft.Azure.Engagement;

-   `Page` を `EngagementPage` に置き換えます。

**Engagement を使用しない場合:**
	
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

> [AZURE.IMPORTANT]ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。

#### XAML ファイル

ページの `.xaml` ファイルを変更します。

-   次の内容を名前空間宣言に追加します。

		xmlns:engagement="using:Microsoft.Azure.Engagement"

-   `Page` を `engagement:EngagementPage` に置き換えます。

**Engagement を使用しない場合:**

		<Page>
		    <!-- layout -->
		    ...
		</Page>

**Engagement を使用する場合:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		    <!-- layout -->
		    ...
		</engagement:EngagementPage >

#### 既定の動作をオーバーライドする

既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。クラスが「Page」サフィックスを使用している場合、Engagement はそれも削除します。

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

これらのメソッドは、ページの `OnNavigatedTo` メソッドから呼び出されます。

### 代替の方法: 手動で `StartActivity()` を呼び出す

`Page` クラスをオーバーロードできない、またはしない場合は、代わりに `EngagementAgent` メソッドを直接呼び出して、アクティビティを開始できます。

ページの `OnNavigatedTo` メソッドの中から `StartActivity` を呼び出すことをお勧めします。

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]セッションは正常に終了してください。
> 
> Windows ユニバーサル SDK は、アプリケーションを終了する際に、自動的に `EndActivity` メソッドを呼び出します。そのため、ユーザーのアクティビティが変更されるたびに `StartActivity` メソッドを呼び出すことを**強く**お勧めします。`EndActivity` メソッドは**決して**呼び出さないでください。このメソッドは、現在のユーザーがアプリケーションを終了したと Engagement サーバーに通知するため、これによってすべてのアプリケーション ログが影響を受けるからです。

##詳細な報告

オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、`EngagementAgent` クラスの別のメソッドを使用します。Engagement API により、Engagement のすべての高度な機能を使用できます。

詳細については、「[How to use the advanced Mobile Engagement tagging API in your Windows Universal app (Windows ユニバーサル アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法)](../mobile-engagement-windows-store-use-engagement-api/)」をご覧ください

##詳細な構成

### 自動クラッシュ レポートを無効にする

Engagement の自動クラッシュ レポート機能を無効にできます。その場合、未処理の例外が発生しても、Engagement は何も行いません。

> [AZURE.WARNING]この機能を無効にする計画がある場合は、アプリで未処理の例外が発生しても、エンゲージメントはクラッシュを報告せず、**さらに**セッションとジョブを終了しないことに注意してください。

自動クラッシュ レポートを無効にするには、宣言した方法に応じて、構成をカスタマイズするだけです。

#### `EngagementConfiguration.xml` ファイルを使用する場合

`<reportCrash>` タグと `</reportCrash>` タグの間で、report crash を `false` に設定します。

#### 実行時に `EngagementConfiguration` オブジェクトを使用する場合

EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### バースト モード

既定では、Engagement サービスはログをリアルタイムで報告します。アプリケーションがログを非常に頻繁に報告する場合、ログをバッファーに格納して、それらすべてを定期的に一度に報告する方が適切です (これを「バースト モード」と呼びます)。

そのためには、次のメソッドを呼び出します。

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は**ミリ秒**単位です。リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。保存できるログは 300 項目に制限されていることに気を付けてください。送信時間が長すぎる場合は、ログがいくつか失われる可能性があります。

> [AZURE.WARNING]また、バーストのしきい値は、1 秒よりも短い期間に設定することはできません。それを試みた場合、SDK はエラーのトレースを表示し、自動的に既定値 (0 秒) に再設定します。これにより、SDK はログをリアルタイムで報告するようになります。

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=Oct15_HO3-->