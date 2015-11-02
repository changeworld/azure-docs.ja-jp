<properties 
	pageTitle="Windows 向けの Application Insights のリリース ノート" 
	description="Windows ストア SDK 用の最新の更新プログラムです。" 
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
	ms.date="09/18/2015" 
	ms.author="sergkanz"/>
 
# Windows Phone と Windows ストア向けの Application Insights SDK のリリース ノート

[Application Insights SDK](app-insights-windows-get-started.md) では、実行中のアプリに関するテレメトリを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信し、その使用状況とパフォーマンスを分析できます。


#### アプリケーションに SDK をインストールするには

「[Windows Phone アプリと Windows ストア アプリ向けの Application Insights を使ってみる](app-insights-windows-get-started.md)」をご覧ください。

#### 最新の SDK にアップグレードするには 

* 実行済みのカスタマイズを維持するには、ApplicationInsights.config をコピーします。
* ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
* インストールされているパッケージを表示するフィルターを設定します。 
* インストール済みの Application Insights パッケージを選択し、[アップグレード] を選択します。
* 新旧のバージョンの ApplicationInsights.config を比較します。旧バージョンに対して行ったカスタマイズをもう一度マージします。
* ソリューションをリビルドします。

## バージョン 1.2

### Windows App SDK

- アプリが再度開かれた後に永続化したテレメトリが送信されるのを阻止していた FileNotFound 例外を修正します。

### コア SDK

- [github](http://github.com/microsoft/ApplicationInsights-dotnet) から出荷された Application Insights SDK の最初のバージョン

## バージョン 1.1

### コア SDK

- SDK では、アプリケーションからの依存関係の呼び出しに関する情報を含む新しいテレメトリ タイプ ```DependencyTelemetry``` が導入されました。
- 新しいメソッド ```TelemetryClient.TrackDependency``` では、アプリケーションからの依存関係の呼び出しに関する情報を送信することができます。

## バージョン 1.0.0

### Windows App SDK

- Windows アプリの新しい初期化。新しい `WindowsAppInitializer` クラスと `InitializeAsync()` メソッドにより、SDK コレクションのブートストラップの初期化が可能になりました。この変更により、以前の ApplicationInsights.config の手法より細かい制御とアプリ初期化パフォーマンスの飛躍的な向上が実現されます。
- DeveloperMode は自動設定されなくなりました。DeveloperMode 動作を変更するには、コード内に指定する必要があります。
- NuGet パッケージでは、ApplicationInsights.config が挿入されません。手動で NuGet パッケージを追加するときに、新しい WindowsAppInitializer を使用することをお勧めします。
- ApplicationInsights.config は `<InstrumentationKey>` を読み取るだけで、他のすべての設定はWindowsAppInitializer 設定の優先設定で無視されます。
- Store Market は SDK によって自動収集されます。
- 多くのバグが修正され、安定性とパフォーマンスが向上しています。

### コア SDK

- ApplicationInsights.config ファイルが不要になり、NuGet パッケージによって追加もされません。構成はコード内で完全に指定できます。
- NuGet パッケージは、ターゲット ファイルをソリューションに追加しません。これにより、デバッグ ビルド時に DeveloperMode の自動設定が削除されます。DeveloperMode をコード内に手動で設定する必要があります。

## バージョン 0.17

### Windows App SDK

- Windows App SDK で、Windows 10 テクニカル プレビューに対して、または VS 2015 RC により作成されたユニバーサル Windows アプリがサポートされるようになりました。

### コア SDK

- TelemetryClient は既定で InMemoryChannel を初期化します。
- 新しい API、`TelemetryClient.Flush()` が追加されました。このフラッシュのメソッドは、そのクライアントに記録されたすべてのテレメトリのアップロードの即時ブロックをトリガーします。これにより、プロセスのシャット ダウン前にアップロードを手動でトリガーできます。
- NuGet パッケージに .Net 4.5 ターゲットが追加されました。このターゲットには外部依存関係がありません (BCL および EventSource 依存関係は削除されました)。

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

<!---HONumber=Oct15_HO4-->