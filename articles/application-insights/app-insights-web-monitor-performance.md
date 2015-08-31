<properties 
	pageTitle="Application Insights を使用したアプリケーションの状態と利用状況の監視" 
	description="Application Insights の使用を開始します。内部設置型または Microsoft Azure アプリケーションの使用状況、可用性、パフォーマンスを分析します。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# Web アプリケーションのパフォーマンスを監視する

*Application Insights はプレビュー段階です。*


アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。[Application Insights][start] はパフォーマンス上の問題や例外を通知し、問題の原因を把握して診断するのに役立ちます。

Application Insights が監視できるのは、オンプレミス、仮想マシン、および Microsoft Azure Websites でホストされる、ASP.NET Web アプリケーションおよび WCF サービスです。


## <a name="setup"></a>パフォーマンス モニターの設定

プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

* [Visual Studio のプロジェクトに Application Insights を追加する][greenbrown] - 推奨されている方法です。パッシブなパフォーマンス モニターに加え、診断ログを挿入したり、利用状況を追跡したりできます。
* [ライブ Websites のパフォーマンスを監視する][redfield] - この場合、アプリケーション プロジェクトを更新したり、Web サイトを再デプロイしたりする必要はありません。
* [Microsoft Azure の Web サイトの場合](../insights-how-to-customize-monitoring.md)、Web サイトの監視レンズで既にメトリックを確認できるようになっています。 

これらの方法のいずれかを使用して、Application Insights の概要ブレードでデータをすばやく確認できます。


## <a name="view"></a>メトリックの確認

タイルをクリックして、詳細を表示したり、より長い期間の結果を表示したりします。たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。


![クリックしてより多くのデータを表示し、時間範囲を選択する](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加してそのメトリックを選択します。

![グラフをクリックし、メトリックを選択する](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [AZURE.NOTE]選択可能な項目をすべて表示するには、**すべてのメトリックのチェック ボックスをオフにしてください**。メトリックはグループに分けられ、グループ内のあるメンバーが選択されると、そのグループのメンバーのみが表示されます。


## <a name="metrics"></a>どのような意味がありますか? パフォーマンス タイルとレポート

多様なパフォーマンス メトリックを取得できます。まず、アプリケーション ブレードに既定で表示されるメトリックから始めます。


### 要求

指定の期間に受け取った HTTP 要求の数です。これを他のレポートにおける結果と比較して、負荷が変化するとアプリケーションの動作がどのように変わるかを確認します。

HTTP 要求には、ページ、データ、画像に関するすべての GET 要求または POST 要求が含まれます。

タイルをクリックして、特定の URL のカウントを取得します。

### 平均応答時間

アプリケーションに入力した Web 要求と返された応答の間の時間を測定します。

各ポイントは移動平均を示しています。多くの要求がある場合、グラフ内の明確なピークや下落以外に、平均から逸脱している要求が存在することがあります。

異常なピークを探します。通常、要求の数が多くなれば応答時間も長くなることが想定されます。上昇の形が不均衡な場合、アプリケーションがリソース制限 (CPU、および CPU が使用するサービス機能など) に達した可能性があります。

タイルをクリックして、特定の URL の時間を取得します。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)


### 最も遅い要求

![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

パフォーマンス チューニングが必要となる可能性がある要求を示します。


### 失敗した要求

![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

受信できない例外がスローされた要求の数。

タイルをクリックして、特定の障害の詳細を表示したり、個別の要求を選択してその詳細を表示したりします。

障害の代表的な例だけが、それぞれの検査用に保持されます。

### その他のメトリック

表示できる他のメトリックを確認するには、グラフをクリックし、すべてのメトリックを選択解除してすべてのメトリック セットを表示します。(i) をクリックし、各メトリック定義を表示します。

![すべてのメトリックを選択解除し、全セットを表示する](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)


いずれかのメトリックを選択すると、同じグラフ上に表示できない他のメトリックは無効になります。

## システム パフォーマンス カウンター

[パフォーマンス カウンター](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)から、いくつかのメトリックを選択できます。Windows にはさまざまなパフォーマンス カウンターが用意されていますが、ユーザーが独自に定義することもできます。

この例では、既定で使用できるパフォーマンス カウンターを表示します。各カウンターに[個別のグラフを追加](app-insights-metrics-explorer.md#editing-charts-and-grids)して、[好みの名前を付けて保存](app-insights-metrics-explorer.md#editing-charts-and-grids)することでグラフに名前を付けました。

![](./media/app-insights-web-monitor-performance/sys-perf.png)


必要なカウンターがプロパティ一覧にない場合は、SDK の収集セットにそれらを追加できます。ApplicationInsights.config を開き、パフォーマンス コレクター ディレクティブを次のように編集します。

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(electronics)#Items Sold" ReportAs="Item sales"/>
      </Counters>
    </Add>

形式は `\Category(instance)\Counter"` です。インスタンスが存在しないカテゴリの場合は、単に `\Category\Counter` です。システムで使用できるカウンターを確認するには、[こちらの説明](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)をご覧ください。

カウンターの名前に、英字、丸かっこ、スラッシュ、ハイフン、アンダー スコア、スペース、ドットの文字以外が含まれている場合は、`ReportAs` が必要です。

インスタンスを指定した場合は、報告されるメトリックの "CounterInstanceName" プロパティとして収集されます。

必要に応じて、同じ効果を持つ次のようなコードを記述できます。

    var perfCollector = new PerformanceCollectorModule();
    perfCollector.Counters = new List<CustomPerformanceCounterCollectionRquest>();
    perfCollector.Counters.Add(new CustomPerformanceCounterCollectionRquest(
      @"\Sales(electronics)#Items Sold", "Items sold"));
    perfCollector.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryModules.Add(perfCollector);



## アラートの設定

すべてのメトリックの異常な値を電子メールで通知するには、アラートを追加します。アカウント管理者または特定の電子メール アドレスのいずれかに電子メールを送信することを選択できます。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

その他のプロパティの前に、リソースを設定します。パフォーマンスまたは使用状況のメトリックにアラートを設定する場合、Web テスト リソースは選択しないでください。

しきい値を入力するように求められたら、単位に注意してください。

*[アラートの追加] ボタンが表示されません。*これは、読み取り専用のアクセス許可しかないグループ アカウントなのでしょうか? アカウント管理者にご確認ください。

## <a name="diagnosis"></a>問題の診断

パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

* [Web テスト][availability]を設定し、Web サイトが停止したり、間違って応答したり、応答速度が低下したりする場合にアラートを送信するようにします。 
* 要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
* コード内に[トレース ステートメントを挿入して、そうしたステートメントを検索する][diagnostic]ことにより、問題の特定に役立てます。

## <a name="next"></a>次のステップ

[Web テスト][availability] - 世界中から定期的に、Web 要求をご使用のアプリケーションに送信します。

[診断トレースを収集して検索する][diagnostic] - トレース呼び出しを挿入し、結果を詳しく調べて問題を特定します。

[利用状況を追跡する][usage] - ご使用のアプリケーションをどれほどのユーザーが使用しているかを調べます。

[トラブルシューティング][qna] - および Q & A

## ビデオ

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=August15_HO8-->