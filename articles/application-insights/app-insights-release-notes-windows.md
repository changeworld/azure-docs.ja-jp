<properties 
	pageTitle="Application Insights のリリース ノート" 
	description="最新の更新プログラム。" 
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
	ms.date="06/18/2015" 
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


## バージョン 0.16 

2015-04-28 プレビュー

- SDK は DNX ターゲット プラットフォームをサポートし、[.NET Core フレームワーク](http://www.dotnetfoundation.org/NETCore5) アプリケーションを監視できるようになりました。
- ```TelemetryClient``` のインスタンスはインストルメンテーション キーをキャッシュしなくなりました。インストルメンテーション キーが ```TelemetryClient``` に明示的に設定されていなかった場合、```InstrumentationKey``` は null を返します。いくらかの利用統計情報が既に収集された後、```TelemetryConfiguration.Active.InstrumentationKey``` を設定すると、問題が解決されます。依存関係コレクター、Web 要求データ コレクション、パフォーマンス カウンター コレクションのような利用統計情報モジュールは新しいインストルメンテーション キーを使用します。

## バージョン 0.15

- 新しいプロパティ ```Operation.SyntheticSource``` が ```TelemetryContext``` で利用できるようになりました。利用統計情報項目を「本物のユーザー トラフィックではない」として設定し、そのトラフィックが生成された背景を指定できるようになりました。このプロパティを設定する例としては、テスト自動化のトラフィックを負荷テストのトラフィックと区別できます。
- チャネル ロジックは「Microsoft.ApplicationInsights.PersistenceChannel」と呼ばれる別個の NuGet に移動されました。既定のチャネルは「InMemoryChannel」という名前になりました。
- 新しいメソッド ```TelemetryClient.Flush``` では、バッファーからの利用統計情報項目を同期的にフラッシュできます。

## バージョン 0.13

使用可能な以前のバージョンのリリース ノートはありません。

<!---HONumber=62-->