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
 
# .NET 向けの Application Insights SDK のリリース ノート

[Application Insights SDK for .NET](app-insights-start-monitoring-app-health-usage.md) では、実行中のアプリに関するテレメトリを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信し、その使用状況とパフォーマンスを分析できます。


#### アプリケーションに SDK をインストールするには

「[Application Insights for .NET を使ってみる](app-insights-start-monitoring-app-health-usage.md)」を参照してください。

#### 最新の SDK にアップグレードするには 

* アップグレード後に、ApplicationInsights.config に対して行ったカスタマイズをもう一度マージする必要があります。カスタマイズしたかどうかが不明な場合は、新しいプロジェクトを作成して Application Insights を追加し、元の .config ファイルと新しいプロジェクトの .config ファイルを比較します。相違点が見つかったら、メモしておきます。
* ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
* インストールされているパッケージを表示するフィルターを設定します。 
* **[Microsoft.ApplicationInsights.Web]**、**[アップグレード]** の順に選択します (これで従属するすべてのパッケージもアップグレードされます)。
* ApplicationInsights.config を前のコピーと比較します。表示される変更の大部分は、モジュールを削除したり、パラメーター化できるようにしたことが原因です。前のファイルに対して行ったカスタマイズをもう一度設定します。
* ソリューションをリビルドします。

## バージョン 0.17
- Framework 4.5 アプリケーションの EventSource NuGet に対する依存関係が削除されました。
- 匿名ユーザーとセッションの Cookie は、サーバー側では生成されません。テレメトリ モジュール ```WebSessionTrackingTelemetryModule``` と ```WebUserTrackingTelemetryModule``` はサポートされなくなり、ApplicationInsights.config ファイルから削除されました。JavaScript SDK の Cookie が適用されます。
- Web SDK では、高負荷のシナリオ用に最適化された永続化チャネルが使用されます。"Spiral of death (悪循環)" の問題が修正されました。"Spiral of death (悪循環)" とは、テレメトリの項目数が急激に増加し、エンドポイントの調整の限界を大幅に超えた場合に、一定時間の経過後に処理が再試行されるものの、その再試行でも調整が発生するような状態を指します。
- 開発者モードは運用環境向けに最適化されています。誤ってそのままにしておいても、追加情報の出力を試行する前ほどの大きな負荷は発生しません。
- 開発者モードが既定で有効になるのは、アプリケーションがデバッグ中の場合のみです。この設定は ```ITelemetryChannel``` インターフェイスの ```DeveloperMode``` プロパティを使用してオーバーライドできます。

## バージョン 0.16 

2015-04-28 プレビュー

- SDK は DNX ターゲット プラットフォームをサポートし、[.NET Core フレームワーク](http://www.dotnetfoundation.org/NETCore5) アプリケーションを監視できるようになりました。
- ```TelemetryClient``` のインスタンスはインストルメンテーション キーをキャッシュしなくなりました。インストルメンテーション キーが ```TelemetryClient``` に明示的に設定されていなかった場合、```InstrumentationKey``` は null を返します。いくらかのテレメトリが既に収集された後、```TelemetryConfiguration.Active.InstrumentationKey``` を設定すると、問題が解決されます。依存関係コレクター、Web 要求データ コレクション、パフォーマンス カウンター コレクションのようなテレメトリ モジュールは新しいインストルメンテーション キーを使用します。

## バージョン 0.15

- 新しいプロパティ ```Operation.SyntheticSource``` が ```TelemetryContext``` で利用できるようになりました。テレメトリ項目を「本物のユーザー トラフィックではない」として設定し、そのトラフィックが生成された背景を指定できるようになりました。このプロパティを設定する例としては、テスト自動化のトラフィックを負荷テストのトラフィックと区別できます。
- チャネル ロジックは「Microsoft.ApplicationInsights.PersistenceChannel」と呼ばれる別個の NuGet に移動されました。既定のチャネルは「InMemoryChannel」という名前になりました。
- 新しいメソッド ```TelemetryClient.Flush``` では、バッファーからのテレメトリ項目を同期的にフラッシュできます。

## バージョン 0.13

使用可能な以前のバージョンのリリース ノートはありません。

 

<!---HONumber=58_postMigration-->