---
title: Windows 向けの Application Insights のリリース ノート
description: Windows ストア SDK 用の最新の更新プログラムです。
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb

---
# Windows Phone と Windows ストア向けの Application Insights SDK のリリース ノート
Application Insights SDK では、実行中のアプリに関するテレメトリを [Application Insights](https://azure.microsoft.com/services/application-insights/) に送信し、その使用状況とパフォーマンスを分析できます。

## バージョン 1.1.1
### Windows SDK
* Windows Phone の Silverlight SDK を使用する場合は、クラッシュ時にハングを修正します。この変更を行うと、WindowsAppInitialier.InitializeAsync(...) の呼び出しの後で、約 2 秒後以降に発生するクラッシュがディスクに保存され、次回のアプリケーションの起動時に送信されます。呼び出し後約 2 秒より前に発生するクラッシュは、無視されます。
* NuGet の依存関係を特定のバージョンのコアと Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3) に設定します。

### コア SDK
* コアは、github で管理されます。コア SDK の将来のリリース ノートは、[github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases) で確認できます。

## バージョン 1.1
### コア SDK
* SDK では、アプリケーションからの依存関係の呼び出しに関する情報を含む新しいテレメトリ タイプ ```DependencyTelemetry``` が導入されました。
* 新しいメソッド ```TelemetryClient.TrackDependency``` では、アプリケーションからの依存関係の呼び出しに関する情報を送信することができます。

## バージョン 1.0.0
### Windows App SDK
* Windows アプリの新しい初期化。新しい `WindowsAppInitializer` クラスと `InitializeAsync()` メソッドにより、SDK コレクションのブートストラップの初期化が可能になりました。この変更により、以前の ApplicationInsights.config の手法より細かい制御とアプリ初期化パフォーマンスの飛躍的な向上が実現されます。
* DeveloperMode は自動設定されなくなりました。DeveloperMode 動作を変更するには、コード内に指定する必要があります。
* NuGet パッケージでは、ApplicationInsights.config が挿入されません。手動で NuGet パッケージを追加するときに、新しい WindowsAppInitializer を使用することをお勧めします。
* ApplicationInsights.config は `<InstrumentationKey>` を読み取るだけで、他のすべての設定はWindowsAppInitializer 設定の優先設定で無視されます。
* Store Market は SDK によって自動収集されます。
* 多くのバグが修正され、安定性とパフォーマンスが向上しています。

### コア SDK
* ApplicationInsights.config ファイルが不要になり、NuGet パッケージによって追加もされません。構成はコード内で完全に指定できます。
* NuGet パッケージは、ターゲット ファイルをソリューションに追加しません。これにより、デバッグ ビルド時に DeveloperMode の自動設定が削除されます。DeveloperMode をコード内に手動で設定する必要があります。

## バージョン 0.17
### Windows App SDK
* Windows App SDK で、Windows 10 テクニカル プレビューに対して、または VS 2015 RC により作成されたユニバーサル Windows アプリがサポートされるようになりました。

### コア SDK
* TelemetryClient は既定で InMemoryChannel を初期化します。
* 新しい API、`TelemetryClient.Flush()` が追加されました。このフラッシュのメソッドは、そのクライアントに記録されたすべてのテレメトリのアップロードの即時ブロックをトリガーします。これにより、プロセスのシャット ダウン前にアップロードを手動でトリガーできます。
* NuGet パッケージに .Net 4.5 ターゲットが追加されました。このターゲットには外部依存関係がありません (BCL および EventSource 依存関係は削除されました)。

## バージョン 0.16
2015-04-28 プレビュー

* SDK は DNX ターゲット プラットフォームをサポートし、[.NET Core フレームワーク](http://www.dotnetfoundation.org/NETCore5) アプリケーションを監視できるようになりました。
* ```TelemetryClient``` のインスタンスはインストルメンテーション キーをキャッシュしなくなりました。インストルメンテーション キーが ```TelemetryClient``` に明示的に設定されていなかった場合、```InstrumentationKey``` は null を返します。いくらかの利用統計情報が既に収集された後、```TelemetryConfiguration.Active.InstrumentationKey``` を設定すると、問題が解決されます。依存関係コレクター、Web 要求データ コレクション、パフォーマンス カウンター コレクションのような利用統計情報モジュールは新しいインストルメンテーション キーを使用します。

## バージョン 0.15
* 新しいプロパティ ```Operation.SyntheticSource``` が ```TelemetryContext``` で利用できるようになりました。テレメトリ項目を「本物のユーザー トラフィックではない」として設定し、そのトラフィックが生成された背景を指定できるようになりました。このプロパティを設定する例としては、テスト自動化のトラフィックを負荷テストのトラフィックと区別できます。
* チャネル ロジックは「Microsoft.ApplicationInsights.PersistenceChannel」と呼ばれる別個の NuGet に移動されました。既定のチャネルは「InMemoryChannel」という名前になりました。
* 新しいメソッド ```TelemetryClient.Flush``` では、バッファーからの利用統計情報項目を同期的にフラッシュできます。

## バージョン 0.13
No release notes for older versions available.

<!---HONumber=AcomDC_0907_2016-->