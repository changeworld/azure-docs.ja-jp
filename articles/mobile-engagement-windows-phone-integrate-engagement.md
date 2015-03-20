<properties 
	pageTitle="Azure モバイル エンゲージメントの Windows Phone SDK との統合" 
	description="エンゲージメントを Windows Phone に統合する方法" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
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


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

# エンゲージメントを Windows Phone に統合する方法

ここでは、Windows Phone アプリケーションでエンゲージメントの分析やモニタリング機能を有効にする簡単な方法について説明します。

次の手順では、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計を計算するためのログ レポートを有効にできます。イベント、エラー、ジョブの統計はアプリケーションに依存するため、それらのログ レポートは、Engagement API を使用して手動で行う必要があります (後述の [How to use the advanced Mobile Engagement tagging API in your Windows Phone app (Windows Phone で高度なモバイル エンゲージメント タグ付け API を使用する方法](../mobile-engagement-windows-phone-use-engagement-api/) 」をご覧ください)。

## サポートされているバージョン

Engagement Windows Phone SDK は、次を対象としたアプリケーションにのみ統合できます:

-   Windows Phone OS 8.0
-   Silgerlight を使用した Windows Phone OS 8.1

Engagement Windows Phone SDK は、Windows Phone 8.0 と 8.1 搭載デバイスに対応しています。

## Engagement SDK を Windows Phone に埋め込む

モバイル エンゲージメントの Nuget パッケージはまだオンラインで利用可能でないため、SDK アーカイブからダウンロードして解凍し、Visual Studio でプロジェクトを右クリックして、[Nuget パッケージの管理]、[設定] の順に選択して新しいパッケージ ソースとして lib フォルダーを追加する必要があります。


> [AZURE.IMPORTANT] NuGet は、.net プロジェクトのメイン パッケージ マネージャーです。NuGet が Engagement SDK の使用に必要なものすべてをダウンロード、追加、コピーします。WP 8 では、クロス プラットフォームの移植目的にサードパーティ製ライブラリ (Microsoft.Bcl.Build v1.0.10 と Microsoft.Bcl ポータビリティ パック v1.1.3) を使用し、NuGet が自動的にそれらもインストールするようメッセージを表示します。詳細については、「[NuGet の Web サイト](http://docs.nuget.org/docs/start-here/overview).」をご覧ください。

## 機能を追加する

Engagement SDK が正常に動作するには、Windows Phone の一部の機能が必要になります。

 `WMAppManifest.xml`  ファイルを開いて、次の機能が `Capabilities`  パネルで宣言されていることを確認します。

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

## Engagement SDK を初期化する

### Engagement の構成

Engagement の構成は、プロジェクトの  `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して次を指定します:

-   `<connectionString>`  と  `<\connectionString>` の間のアプリケーションの接続文字列。

代わりに実行時に指定する場合、エンゲージメント エージェントの初期化前に次のメソッドを呼び出すことができます。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

### Engagement の初期化

新しいプロジェクトを作成すると、`App.xaml.cs`  ファイルが生成されます。このクラスは、 `Application` から継承し、多くの重要なメソッドが含まれています。また、Engagement SDK の初期化にも使用されます。

`App.xaml.cs` を変更します:

-   `using`  ステートメントに追加します :

			using Microsoft.Azure.Engagement;

-   `EngagementAgent.Instance.Init` を `Application_Launching`  メソッドに挿入します :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   `EngagementAgent.Instance.OnActivated` を `Application_Activated` メソッドに挿入します :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Engagement の初期化をアプリケーションの他の部分に追加することはお勧めしません。ただし、`EngagementAgent.Instance.Init` メソッドは UI スレッドではなく専用スレッドで実行する点にご注意ください。

## 基本的なレポート

### 推奨メソッド: `PhoneApplicationPage` クラスをオーバーロードする

Engagement でユーザー、セッション、アクティビティ、クラッシュ、テクニカルの統計を計算するのに必要なすべてのログ レポートを有効にするには、すべての `PhoneApplicationPage` サブクラスを `EngagementPage` クラスから継承させます。

アプリケーションのページでこれを行う方法の例を次に示します。アプリケーションのすべてのページに対して同じ処理を実行できます。

#### C\# Source file

ページの `.xaml.cs` ファイルを変更します :

-   `using` ステートメントに追加します :

			using Microsoft.Azure.Engagement;

-   `PhoneApplicationPage` を `EngagementPage` に置き換えます:

**Engagement なし:**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**Engagement あり:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] ページが `OnNavigatedTo` メソッドから継承する場合は、`base.OnNavigatedTo(e)` の呼び出しに注意します。呼び出しが行われないと、アクティビティがレポートされません。実際には、`EngagementPage` は `OnNavigatedTo` メソッド内の `StartActivity` を呼び出します。

#### XAML ファイル

ページの `.xaml` ファイルを変更します :

-   名前空間宣言に追加します :

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   `phone:PhoneApplicationPage` を `engagement:EngagementPage` に置き換えます:

**Engagement なし:**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**Engagement あり:**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### 既定の動作をオーバーライドします。

既定では、ページのクラス名は追加なしでアクティビティ名として報告されます。クラスで "Page" サフィックスが使用されている場合、Engagement がそれも削除します。

既定の動作の名前をオーバーライドする場合は、次をコードに追加します。

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

アクティビティと共に追加情報をレポートする場合は、次をコードに追加します。

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

追加情報の詳細については、[ここ]をご覧ください(../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters)。

これらのメソッドは、ページの  `OnNavigatedTo` メソッド内から呼び出されます。

### 代替メソッド:  `StartActivity()` を手動で呼び出す

 `PhoneApplicationPage` クラスをオーバーロードできない場合やしたくない場合は、代わりに  `EngagementAgent` メソッドを直接呼び出してアクティビティを開始できます。

 `StartActivity` は PhoneApplicationPage の  `OnNavigatedTo` メソッド内で呼び出すことをお勧めします。

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] セッションは正常に終了してください。
>
> アプリケーションを閉じると、Windows Phone SDK が自動的に  `EndActivity` メソッドを呼び出します。そのため、ユーザーのアクティビティが変化するごとに  `StartActivity` メソッドを呼び出し、 `EndActivity` メソッドは**決して**呼び出さないようにすることを**強く**お勧めします。このメソッドを呼び出すと現在のセッションを強制的に終了します。

## 高度なレポート

必要に応じて、アプリケーション固有のイベント、エラー、ジョブをレポートする場合は、 `EngagementAgent` クラスにある別のメソッドを使用できます。Engagement API では Engagement のすべての高度な機能を使用できます。

詳細については、[How to use the advanced Mobile Engagement tagging API in your Windows Phone app (Windows Phone で高度なモバイル エンゲージメント タグ付け API を使用する方法)](../mobile-engagement-windows-phone-use-the-engagement-api/) をご覧ください。

## 詳細な構成

### 自動クラッシュ レポートを無効にする

Engagement の自動クラッシュ レポート機能を無効にできます。無効にすると、未処理の例外が発生したときに Engagement による処理は起こりません。

> [AZURE.WARNING] この機能を無効にする場合は、アプリで未処理のクラッシュが発生した場合に Engagement からクラッシュを送信せず、**さらに**セッションとジョブも閉じられませんのでご注意ください。

自動クラッシュ レポートを無効にするには、宣言されている方法に基づいて構成をカスタマイズします:

#### `EngagementConfiguration.xml` ファイルから

クラッシュのポートを `<reportCrash>` と `</reportCrash>` タグの間で  `false` に設定します。

#### 実行時の  `EngagementConfiguration` オブジェクトから

EngagementConfiguration オブジェクトを使用してクラッシュのレポートを false に設定します。

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### バースト モード[ベータ版]

既定では、Engagement サービスはリアルタイムでログをレポートします。アプリケーションが非常に頻繁にログをレポートする場合、ログをバッファーに格納して、それらをまとめて定期的にレポートするようお勧めします (これを「バースト モード」と呼びます）。

メソッドを呼び出します:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数は、**ミリ秒**の値です。パラメーターなしでメソッドを呼び出すか、0 の値を使用してメソッドを呼び出せば、いつでもリアルタイムのログ記録を再度有効にできます。

バースト モードでは、バッテリの寿命をわずかに延ばすことができますが、すべてのセッションとジョブの実行時間がバーストのしきい値に丸められる (バーストのしきい値よりも短いセッションとジョブが表示されない場合がある) という Engagement Monitor への影響があります。バーストのしきい値は、30000 (30 秒) よりも短くすることをお勧めします。

> [AZURE.WARNING] バーストのしきい値は、1 秒よりも短くすることはできません。1 秒未満にしようとすると、SDK でエラーのトレースが表示され、自動的に既定値の 0 秒にリセットされます。これにより、SDK でリアルタイムのログ レポートが開始します。

<!--HONumber=47-->
