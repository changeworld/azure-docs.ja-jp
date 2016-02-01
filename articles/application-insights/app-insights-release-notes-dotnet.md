<properties 
	pageTitle="Application Insights for .NET のリリース ノート" 
	description=".NET SDK 用の最新の更新プログラム。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# .NET 向けの Application Insights SDK のリリース ノート

[Application Insights SDK for .NET](app-insights-asp-net.md) では、実行中のアプリに関するテレメトリを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信し、その使用状況とパフォーマンスを分析できます。


#### アプリケーションに SDK をインストールするには

「[Application Insights for .NET を使ってみる](app-insights-asp-net.md)」をご覧ください。

#### 最新の SDK にアップグレードするには 

* アップグレード後に、ApplicationInsights.config に対して行ったカスタマイズをもう一度マージする必要があります。カスタマイズしたかどうかが不明な場合は、新しいプロジェクトを作成して Application Insights を追加し、元の .config ファイルと新しいプロジェクトの .config ファイルを比較します。相違点が見つかったら、メモしておきます。
* ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
* インストールされているパッケージを表示するフィルターを設定します。 
* **[Microsoft.ApplicationInsights.Web]**、**[アップグレード]** の順に選択します (これで従属するすべてのパッケージもアップグレードされます)。
* ApplicationInsights.config を前のコピーと比較します。表示される変更の大部分は、モジュールを削除したり、パラメーター化できるようにしたことが原因です。前のファイルに対して行ったカスタマイズをもう一度設定します。
* ソリューションをリビルドします。

## バージョン 2.0.0-beta4

- UseSampling および UseAdaptiveSampling 拡張メソッドの Microsoft.ApplicationInsights.Extensibility への移行
- ユニバーサル Windows Phone とストアのアプリケーションのサポートの削除
- ```DependencyTelemetry``` が更新され、新しいプロパティ ```ResultCode``` と ```Id``` が追加されました。```ResultCode``` は、HTTP の依存関係の HTTP 応答コードと、SQL の依存関係のエラー コードを提供するために使用されます。```Id``` は、コンポーネント間の関連付けに使用されます。 
- ```ServerTelemetryChannel``` がプログラムで初期化されている場合、```ServerTelemetryChannel.Initialize()``` メソッドの呼び出しに必要です。そうでない場合、永続ストレージは初期化されません (つまり、一時的な接続の問題が発生したときにテレメトリを送信できない場合、テレメトリはドロップされます)。
- ```ServerTelemetryChannel``` にコードまたは構成を介して設定できる新しいプロパティ ```StorageFolder``` が追加されました。このプロパティが設定されている場合、Application Insights は一時的な接続の問題が発生したときに送信されなかったテレメトリを指定の場所に格納します。このプロパティが設定されていない、または指定のフォルダーにアクセスできない場合、Application Insights は以前のように LocalAppData フォルダーまたは Temp フォルダーへの格納を試行します。
- ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder``` 拡張メソッドが削除されました。このメソッドの代わりに ```TelemetryConfiguration.TelemetryProcessorChainBuilder``` インスタンス メソッドを使用します。
- ```TelemetryConfiguration``` クラスに ```TelemetryProcessors``` コレクションへの読み取り専用アクセスを提供する新しいプロパティ ```TelemetryProcessors``` が追加されました。
- ```Use```、```UseSampling```、および ```UseAdaptiveSampling``` は構成から読み込まれた ```TelemetryProcessors``` を保持します。
- すぐに使用できる 2 つのテレメトリ プロセッサ (ユーザー エージェント フィルターのテレメトリ プロセッサと要求ハンドラーのテレメトリ プロセッサ) が構成ファイルに用意されています。それらの動作はカスタマイズできます。フィルターで除外するユーザー エージェント文字列を AI.config ファイルに追加できます。既定では、```AllwaysOn``` ユーザー エージェント文字列がフィルターで除外されています。現在の動作では、大文字小文字を区別しない完全一致比較を使用して、ユーザー エージェント文字列と構成ファイル内の文字列を比較します。また、フィルターで除外する要求のハンドラーの一覧をカスタマイズできます。 
- 依存する Microsoft.ApplicationInsights.Agent.Intercept NuGet のバージョンが 1.2.1 に更新されました。SQL の依存関係コレクションのバグ修正が含まれています。

## バージョン 2.0.0-beta3

- [アダプティブ サンプリング](app-insights-sampling.md)が、サーバーのテレメトリ チャネルで既定で有効になっています。 
- ```UseSampling``` の署名が、テレメトリ プロセッサの ```Use``` へのその他の呼び出しとのチェーンを可能にするように修正されました。  
- プロパティ ```Request.ID``` が復活しました。```OperationContext``` にエンド ツー エンドの相関関係のプロパティ ```ParentId``` が含まれるようになりました。
- ```TimestampTelemetryInitializer``` が削除されました。タイムスタンプが ```TelemetryClient``` により自動的に追加されます。
- 操作の相関関係を有効にするため、```OperationCorrelationTelemetryInitializer``` が既定で追加されます。
- ```OperationIdTelemetryInitializer``` の代わりに ```OperationCorrelationTelemetryInitializer``` が使用されます。
- ユーザー エージェントが、既定で収集されなくなります。ユーザー エージェントのテレメトリ初期化子が削除されました。
- ```DependencyTelemetry.Async``` フィールドは、依存関係コレクター テレメトリ モジュールによって収集されません。 
- 静的コンテンツと診断の要求は、要求テレメトリ モジュールによって収集されません。```RequestTrackingTelemetryModule``` コレクションの ```HandlersToFilter``` を使用して、特定の http ハンドラーによって生成された要求を除外します。 
- 自動生成された要求テレメトリには、HttpContext 拡張メソッド System.Web.HttpContextExtension.GetRequestTelemetry を通じてアクセスできます。  


## バージョン 2.0.0-beta2
- ITelemetryProcessor のサポート、コードまたは構成ファイルを使用して構成する機能が追加されました。[SDK でのカスタム フィルターの有効化](app-insights-api-filtering-sampling/#filtering)
- コンテキストの初期化子が削除されました。代わりに、[テレメトリの初期化子]( https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering)を使用します。
- Application Insights for .Net framework 4.6 が更新されました。 
- カスタム イベント名に最大 512 文字まで使用できるようになりました。
- プロパティ ```OperationContext.Name``` の名前が ```RootName``` に変更されました。
- プロパティ ```RequestTelemetry.Id``` が削除されました。
- 新しい RequestTelemetry を作成する場合、RequestTelemetry のプロパティ ```Id``` と ```Context.Operation.Id``` は初期化されません。
- ```RequestTelemetry.Name``` は初期化されなくなりました。代わりに、```RequestTelemetry.Context.Operation.Name``` が使用されます。
- 要求のモニタリングで、応答コード 401 は通常の認証ハンドシェイクの一部であり、これにより要求は成功します。
- UI スレッドから InMemoryChannel (既定のチャネル) を初期化する際に UI スレッドがロック状態になる問題を解消します。これにより、WPF アプリケーションでの UI フリーズの問題が解消します。
 
## Version 2.0.0-beta1
- 必須フィールドの一部が指定されていない場合は、TrackDependency によって有効な JSON が生成されます。
- 冗長なプロパティの ```RequestTelemetry.ID``` は、```RequestTelemetry.Operation.Id``` のプロキシのみになりました。
- 新しい ```ISupportSampling``` インターフェイスが導入され、ほとんどのデータ項目の型で明示的に実装できるようになりました。
- DependencyTelemetry の ```Count``` プロパティが廃止としてマークされました。代わりに ```SamplingPercentage``` を使用してください。
- ```CloudContext``` が新しく導入され、そこに ```DeviceContext``` から ```RoleName``` プロパティおよび ```RoleInstance``` プロパティが移動しました。
- 認証されたユーザー ID を指定する ```UserContext``` の新しい ```AuthenticatedUserId``` プロパティが導入されました。
- Javascript SDK によって設定された認証済みユーザーのコンテキストを初期化する `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`、`Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` が追加されました。
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` とその実装としての固定比率のサンプリングのサポートが追加されました。
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` が追加され、一連の `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` を使用して `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` を作成できるようになりました。

## バージョン 1.2

- ASP.NET ライブラリへの依存関係を持たないテレメトリ初期化子が、`Microsoft.ApplicationInsights.Web` から新しい依存関係 nuget である `Microsoft.ApplicationInsights.WindowsServer` に移動されました。
- `Microsoft.ApplicationInsights.Web.dll` は `Microsoft.AI.Web.dll` に名前が変更されました。
- `Microsoft.ApplicationInsights.Web.TelemetryChannel` nuget は `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel` に名前が変更されました。`Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` アセンブリは `Microsoft.AI.ServerTelemetryChannel.dll` に名前が変更されました。`Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` クラスは `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` に名前が変更されました。
- Web SDK の一部であるすべての名前空間が、`Extensibility` 部分を除外するように変更されました。これには、ApplicationInsights.config 内のすべてのテレメトリ初期化子と web.config 内の `ApplicationInsightsWebTracking` モジュールが含まれます。
- ランタイム インストルメンテーション エージェント (Status Monitor または Azure WebSite 拡張機能を通して有効にします) を使用して収集される依存関係は、スレッドに HttpContext.Current が存在しない場合は非同期としてマークされません。
- `DependencyTrackingTelemetryModule` の `SamplingRatio` プロパティは何もせず、廃止とマークされています。
- `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` アセンブリは `Microsoft.AI.PerfCounterCollector` に名前が変更されました。
- Web SDK とデバイス SDK でいくつかの小さなバグが修正されています。


## バージョン 1.1

- 新しいテレメトリ タイプ `DependencyTelemetry` が追加されました。これを使用して、アプリケーションからの依存関係の呼び出し (SQL、HTTP 呼び出しなど) に関する情報を送信できます。
- 依存関係の呼び出しに関する情報を送信するための新しいオーバーロード メソッド `TelemetryClient.TrackDependency` が追加されました。
- TelemetryConfiguration.CreateDefault の使用時に診断モジュールによってスローされる NullReferenceException が修正されました。

## バージョン 1.0

- テレメトリの初期化子とテレメトリ モジュールを別々のサブ名前空間からルートの `Microsoft.ApplicationInsights.Extensibility.Web` 名前空間に移動しました。
- `Microsoft.ApplicationInsights.Extensibility.Web` 名前空間の名前に既に含まれているため、"Web" プレフィックスをテレメトリの初期化子とテレメトリ モジュールの名前から削除しました。
- `DeviceContextInitializer` を `Microsoft.ApplicationInsights` アセンブリから `Microsoft.ApplicationInsights.Extensibility.Web` アセンブリに移動し、`ITelemetryInitializer` に変換しました。
- NuGet パッケージの名前との整合性のため、名前空間とアセンブリ名を `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` から `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` に変更しました。
- 名前 `RemoteDependencyModule` が `DependencyTrackingTelemetryModule` に変わります。
- 名前 `CustomPerformanceCounterCollectionRequest` が `PerformanceCounterCollectionRequest` に変わります。

## バージョン 0.17
- Framework 4.5 アプリケーションの EventSource NuGet に対する依存関係が削除されました。
- 匿名ユーザーとセッションの Cookie は、サーバー側では生成されません。 アプリのユーザーおよびセッションの追跡を実装するために JS SDK によるインストルメンテーションが必要になりました。JavaScript SDK の Cookie は引き続き適用されます。テレメトリ モジュール ```WebSessionTrackingTelemetryModule``` と ```WebUserTrackingTelemetryModule``` はサポートされなくなり、ApplicationInsights.config ファイルから削除されました。この変更により、ユーザー数とセッション数の大幅な再計算が発生する場合があることに注意してください。これは、ユーザーが開始したセッションのみがカウントされるようになったためです。
- OSVersion の値が SDK により既定で設定されなくなりました。空の場合、OS と OSVersion が Application Insights のパイプラインによってユーザー エージェントに基づいて計算されます。 
- Web SDK では、高負荷のシナリオ用に最適化された永続化チャネルが使用されます。"Spiral of death (悪循環)" の問題が修正されました。"Spiral of death (悪循環)" とは、テレメトリの項目数が急激に増加し、エンドポイントのスロットルの限界を大幅に超えた場合に、一定時間の経過後に処理が再試行されるものの、その再試行でも調整が発生するような状態を指します。
- 開発者モードは実稼働用に最適化されています。誤ってそのままにしておいても、追加情報の出力を試行する前ほどの大きな負荷は発生しません。
- 開発者モードが既定で有効になるのは、アプリケーションがデバッグ中の場合のみです。この設定は ```ITelemetryChannel``` インターフェイスの ```DeveloperMode``` プロパティを使用してオーバーライドできます。

## バージョン 0.16 

2015-04-28 プレビュー

- SDK は DNX ターゲット プラットフォームをサポートし、[.NET Core フレームワーク](http://www.dotnetfoundation.org/NETCore5) アプリケーションを監視できるようになりました。
- ```TelemetryClient``` のインスタンスはインストルメンテーション キーをキャッシュしなくなりました。インストルメンテーション キーが ```TelemetryClient``` に明示的に設定されていなかった場合、```InstrumentationKey``` は null を返します。いくらかの利用統計情報が既に収集された後、```TelemetryConfiguration.Active.InstrumentationKey``` を設定すると、問題が解決されます。依存関係コレクター、Web 要求データ コレクション、パフォーマンス カウンター コレクションのような利用統計情報モジュールは新しいインストルメンテーション キーを使用します。

## バージョン 0.15

- 新しいプロパティ ```Operation.SyntheticSource``` が ```TelemetryContext``` で利用できるようになりました。テレメトリ項目を「本物のユーザー トラフィックではない」として設定し、そのトラフィックが生成された背景を指定できるようになりました。このプロパティを設定する例としては、テスト自動化のトラフィックを負荷テストのトラフィックと区別できます。
- チャネル ロジックは「Microsoft.ApplicationInsights.PersistenceChannel」と呼ばれる別個の NuGet に移動されました。既定のチャネルは「InMemoryChannel」という名前になりました。
- 新しいメソッド ```TelemetryClient.Flush``` では、バッファーからの利用統計情報項目を同期的にフラッシュできます。

## バージョン 0.13

使用可能な以前のバージョンのリリース ノートはありません。

 

<!----HONumber=AcomDC_0121_2016-->