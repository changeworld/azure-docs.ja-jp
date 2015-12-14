<properties 
	pageTitle="Application Insights の価格とクォータの管理" 
	description="必要な価格プランを選択します" 
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
	ms.date="11/18/2015" 
	ms.author="awills"/>

# Application Insights の価格とクォータの管理

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights][start] の[価格][pricing]は、アプリケーションごとのデータ量に基づきます。真の意味で Free レベルがあり、いくつかの制限はありますが、ほとんどの機能を利用できます。

各 Application Insights リソースは個々のサービスとして課金され、Azure のサブスクリプションの課金内容に加えられます。

[「料金プラン」を参照してください][pricing]。

## Application Insights リソースのクォータと料金プランの確認

アプリケーション リソースの設定からクォータと価格のブレードを開くことができます。

![設定の選択、クォータと価格](./media/app-insights-pricing/01-pricing.png)

選択した料金プランは次に影響します。

* [月間クォータ](#monthly-quota) - 月ごとに分析できるテレメトリの量。
* [データ速度](#data-rate) - アプリで処理可能なデータの最大速度。
* [保有期間](#data-retention) - 表示用にデータを Application Insights ポータルに保持する期間。
* [連続エクスポート](#continuous-export) - 他のツールやサービスにデータをエクスポートするかどうかを指定します。

これらの制限は、Application Insights のリソースごとに個別に設定されます。

### 無料の Premium 評価版

最初に新しい Application Insights リソースを作成するときは、Free レベルから開始します。

いつでも 30 日間無料の Premium 評価版に切り替えることができます。これにより、Premium レベルを体験できます。30 日が経過すると、明示的に別のレベルを選択していなければ、以前のレベルに自動的に戻ります。評価期間中はいつでもご希望のレベルを選択できますが、30 日の期間が終了するまでは無料試用版を取得できます。


## 月間クォータ

* 毎月、アプリケーションは、指定された量のテレメトリを Application Insights に送ることができます。実際の数値については、「[料金プラン][pricing]」をご確認ください。 
* クォータは、選択した価格レベルによって異なります。
* クォータは、毎月 1 日の午前 0 時 UTC からカウントされます。
* データ ポイントのグラフは、今月クォータのどのくらいが使用されたかを示しています。
* クォータは、*データ ポイント*単位で測定されます。 単一のデータ ポイントは、追跡メソッドの 1 つの呼び出しで、コードで明示的に呼び出されるか、標準テレメトリ モジュールのいずれかによって呼び出されます。データ ポイントには次の項目が含まれます。
 * [診断検索](app-insights-diagnostic-search.md)で確認される各行 
 * パフォーマンス カウンターなどの[メトリック](app-insights-metrics-explorer.md)の生の各測定値(グラフに表示されるポイントは、通常は複数の生のデータ ポイントを集計したものです)
 * [Web テスト (可用性)](app-insights-monitor-web-app-availability.md) グラフの各ポイント 
* *セッション データ*は、クォータにカウントされません。これには、ユーザー、セッション、環境、デバイスのデータの数が含まれます。


### 超過料金

アプリケーションの送信量が月間クォータを超える場合、次の操作を実行できます。

* 追加データ分を支払います。詳細については、「[料金プラン][pricing]」をご覧ください。このオプションは、事前に選択できます。このオプションは、無料の価格レベルでは選択できません。
* 価格レベルをアップグレードします。
* 何もしません。セッション データは引き続き記録されますが、それ以外のデータは、診断の検索やメトリックス エクスプローラーに表示されません。


### 送信するデータ量はどのくらいですか。

価格ブレードの下部にあるグラフは、アプリケーションのデータ ポイント量を示しています。これは、データ ポイントの種類でグループ化されます (このグラフはメトリック エクスプローラーで作成することもできます)。

![価格ブレードの下部](./media/app-insights-pricing/03-allocation.png)

グラフをクリックすると、詳細が表示されます。時間範囲の詳細を入手するには、グラフをドラッグして [+] をクリックします。


## データ速度

月間クォータの他にも、データ速度での調整の上限もあります。上限は、無料の[価格レベル][pricing]では、5 分間の平均が 1 秒あたり 200 データ ポイント、有料レベルでは、1 分間の平均が 1 秒あたり 500 データ ポイントになります。

次の 3 つのバケットは別々にカウントされます。

* [TrackTrace 呼び出し](app-insights-api-custom-events-metrics.md#track-trace)と[キャプチャしたログ](app-insights-asp-net-trace-logs.md)
* [例外](app-insights-api-custom-events-metrics.md#track-exception) (1 秒あたり 50 ポイントに制限されます)
* その他すべてのテレメトリ (ページ ビュー、セッション、要求、依存関係、メトリック、カスタム イベント、Web テストの結果)

アプリが数分にわたり制限を超えてデータを送信すると、データの一部が破棄される場合があります。この状況が発生したことを通知する警告が表示されます。

### データ速度を下げるためのヒント

調整の上限に対処する方法を次に示します。

* [サンプリング](app-insights-sampling.md)を使用します。このテクノロジは、メトリックや、検索で関連するアイテム間を移動する機能を損なうことなく、データ レートを削減します。
* [ApplicationInsights.config を編集](app-insights-configuration-with-applicationinsights-config.md)し、不要なコレクション モジュールを無効にします。たとえば、パフォーマンス カウンターや依存関係のデータが重要ではないと判断した場合などに検討します。
* Pre-aggregate metrics.TrackMetric への呼び出しをアプリに配置した場合、平均計算と測定のバッチの標準偏差を受け入れるオーバーロードを使用して、トラフィックを減らすことができます。また、[事前集計パッケージ](https://www.myget.org/gallery/applicationinsights-sdk-labs)を使用することもできます。 


### 名前の制限

1.	アプリケーションに対して最大 200 の一意のメトリックの名前と 200 の一意のプロパティの名前。メトリックには、TrackMetric を通じて送信されるデータと、イベントなどの他のデータ型の測定値が含まれます。[メトリックとプロパティの名前][api]は、データ型にスコープが制限されず、インストルメンテーション キーごとにグローバルです。
2.	各プロパティに対する一意の値は 100 未満であり、[プロパティ][apiproperties]は、フィルタリングとグループ化のみに使用できます。一意の値が 100 を超えた後も、プロパティは検索とフィルタリングに使用できますが、フィルター処理には使用できなくなります。
3.	要求名やページの URL などの標準プロパティは、1 週間あたりの 1000 の一意な値に制限されます。1000 の一意の値を超えると、追加の値は "その他の値" としてマークされます。 元の値は、全文テキスト検索とフィルタリングに引き続き使用できます。

## データの保持

価格レベルによって、データがポータルに保持される期間が決まるため、設定可能な時間範囲も判断できます。


* 生データ ポイント (つまり、診断検索で確認できるインスタンス) は、7 ～ 30 日間です。
* 集計されたデータ (つまり、メトリックス エクスプローラーに表示されるカウント、平均、その他の統計データ) は、1 分の詳細度であれば 30 日、(種類に応じて) 1 時間または 1 日の詳細度であれば少なくとも 13 か月の期間にわたって保持されます。




## Azure へのサブスクリプションの課金内容の確認

Application Insights の課金は Azure の課金内容に加えられます。Azure ポータルの [課金] か [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。

![サイド メニューで、[課金] を選択します。](./media/app-insights-pricing/02-billing.png)

## 制限の概要

[AZURE.INCLUDE [Application Insights の制限事項](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_1203_2015-->