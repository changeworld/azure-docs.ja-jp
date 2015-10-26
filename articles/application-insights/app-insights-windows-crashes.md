<properties 
	pageTitle="Application Insights による Windows ストア アプリと Windows Phone アプリでのクラッシュの検出と診断" 
	description="Application Insights を使用して Windows デバイス アプリのパフォーマンスの問題を分析します。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="awills"/>

# Application Insights による Windows ストア アプリと Windows Phone アプリでのクラッシュの検出と診断

*Application Insights はプレビュー段階です。*

自分のアプリでユーザーがクラッシュに遭遇した場合は、それをすばやく把握し、何が起きたのかを詳しく知る必要があります。With Application Insights, you can monitor how frequently crashes occur, get alerts when they occur, and investigate reports of individual incidents.

"クラッシュ" とは、キャッチされない例外に起因してアプリケーションが終了することを意味します。アプリが例外をキャッチした場合は [TrackException API][apiexceptions] でそれをレポートできますが、動作は継続します。その場合、クラッシュとしてはログに記録されません。


## クラッシュ発生頻度の監視

この操作をまだ行っていない場合は、[Application Insights をアプリ プロジェクトに追加][windows]して再発行ください。

クラッシュは [Application Insights ポータル][portal]のアプリケーションの概要ブレードに表示されます。

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

表示されている時間範囲はグラフごとに編集できます。


## クラッシュを検出するアラートの設定

![クラッシュのグラフから、[アラート ルール]、[アラートの追加] の順にクリックします](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## クラッシュの診断

アプリの一部のバージョンが他より頻繁にクラッシュしているかどうかを確認するには、次のようにクラッシュのグラフをクリックして進み、アプリケーションのバージョン別に分割します。

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


クラッシュを引き起こしている例外を検索するには、[診断検索] を開きます。例外に的を絞るには、次のように他のテレメトリの種類を削除することをお勧めします。

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[診断検索でのフィルター処理の詳細][diagnostic]を確認してください。
 

関連するプロパティやスタック トレースなどの詳細を表示するには、いずれかの例外をクリックします。

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

次のように、発生日時がその例外に近い他の例外およびイベントを表示します。


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## トレース ログとイベントの挿入

問題の診断に役立てるため、[Application Insights でトレース呼び出しの挿入とログの検索][diagnostic]を実行できます。

## <a name="debug"></a>デバッグ モードとリリース モードの比較

#### デバッグ

デバッグ モードでビルドする場合、イベントは生成されるとすぐに送信されます。インターネット接続が切断され、接続が復旧する前にアプリを終了した場合、オフラインのテレメトリは破棄されます。

#### リリース

リリース構成でビルドする場合、イベントはデバイスに格納され、アプリケーション再開時に送信されます。Data is also sent on the application's first use.起動時にインターネット接続されていない場合は、前のテレメトリだけでなく、現在のライフサイクルのテレメトリも格納され、次の再開時に送信されます。

## <a name="next"></a>次のステップ

[Application Insights を使用した問題の検出、トリアージ、および診断][detect]

[Application Insights API][api]

[診断ログのキャプチャ][trace]

[トラブルシューティング](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO3-->