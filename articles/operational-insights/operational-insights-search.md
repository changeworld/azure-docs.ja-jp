<properties 
	pageTitle="オペレーション インサイトの検索機能" 
	description="探しているデータは、Azure オペレーション インサイトの検索機能で見つけることができます。" 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="banders"/>

# オペレーション インサイトの検索機能
Microsoft Azure オペレーション インサイトの核となる機能は検索です。ユーザーは検索を通じて、環境内のさまざまなソースからのコンピューター データを組み合わせたり関連付けたりすることができます。また、インテリジェンス パックから検索機能を利用し、特定の問題点に関するメトリックを得ることができます。


## データの検索
探しているデータは、Azure オペレーション インサイトの**検索**機能で見つけることができます。 

![検索タイルの画像](./media/operational-insights-search/overview-search.png)

検索クエリは **[検索]** ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。検索結果に対して変換やフィルター処理、レポート作成などを実行する高度なクエリを作成することもできます。

![検索ページの画像](./media/operational-insights-search/search-01.png)

ほとんどの検索ページには、一般的な検索クエリが表示されます。オペレーション インサイト コンソールのあらゆる場所で、タイルをクリックするか、他の項目にドリル インし、**[検索]** を使用してその項目の詳細を表示することができます。
ファセット コントロールとフィルター コントロールを使用すれば、検索結果を絞り込むことができます。オートコンプリートによる入力支援機能を備えているため、構文を覚える手間が省け、必要な結果をすばやく得ることができます。

##Azure ライブラリのトピック 
- [検索構文のリファレンス](https://msdn.microsoft.com/library/azure/dn884655.aspx  "Search Syntax Reference")
- [検索フィールドとファセットのリファレンス](https://msdn.microsoft.com/library/azure/dn884648.aspx "Search field and facet reference")  
 
 
## 検索構文に関するブログ記事とチュートリアル
- [Azure オペレーション インサイトの検索機能の使用方法:第 1 部 - ビッグ データをフィルター処理する方法](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-search-first-steps-how-to-filter-data-part-i.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 2 部 - 論理演算子、時間ディメンション、数字、範囲を使用したフィルタリングの詳細](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-search-how-to-part-ii-more-on-filtering-using-boolean-operators-and-the-time-dimension.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 3 部 - 検索結果の操作: パイプライン "|" と検索コマンド](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-search-how-to-part-iii-manipulating-results-the-pipeline-and-search-commands.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 4 部 - MEASURE コマンドの概要](http://blogs.msdn.com/b/dmuscett/archive/2014/10/29/operational-insights-search-how-to-part-iv-introducing-the-measure-command.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 5 部 - Measure コマンドでの統計関数 Max() および Min()](http://blogs.msdn.com/b/dmuscett/archive/2014/10/29/azure-operational-insights-search-howto-part-v-max-and-min-statistical-functions-with-measure-command.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 6 部 - Measure Avg() と Type=PerfHourly の解説](http://blogs.msdn.com/b/dmuscett/archive/2014/10/31/azure-operational-insights-search-how-to-part-vi-measure-avg-and-an-exploration-of-type-perfhourly.aspx)
- [Azure オペレーション インサイトの検索機能の使用方法:第 7 部 - Measure Sum() と Where コマンド](http://blogs.msdn.com/b/dmuscett/archive/2014/11/10/azure-operational-insights-search-hot-to-part-vii-measure-sum-and-where-command.aspx)

## ブログ記事 - 検索機能の使用例
- [Microsoft Azure オペレーション インサイトでの W3C IIS ログ検索](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Azure オペレーション インサイトの検索とダッシュボードで SQL バックアップ エラーを監視する](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [IIS 管理パックのイベント アラート ルールでオペレーション インサイトの検索と同等の処理を行う](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [オペレーション インサイトの便利な検索クエリ集](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)


<!--HONumber=52--> 