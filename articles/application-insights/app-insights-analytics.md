---
title: "Analytics の使用 - Azure Application Insights の強力な検索およびクエリ　ツール | Microsoft Docs"
description: "Application Insights の強力な診断検索ツールである Analytics の概要。 "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 80a9e248ca50c11ef61a5c50c4986c4f8f4ead9d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="analytics-in-application-insights"></a>Application Insights の Analytics
Analytics は、[Application Insights](app-insights-overview.md) の強力な検索およびクエリのツールです。 Analytics は Web ツールであるため、セットアップは不要です。 Application Insights をお使いのアプリの一つで構成済みの場合、アプリの [[概要]](app-insights-dashboards.md) ブレードで Analytics を開いてアプリのデータを分析できます。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics/001.png)

無料のデモ環境である [Analytics プレイグラウンド](https://go.microsoft.com/fwlink/?linkid=859557)を使って、多くのサンプル データを利用することもできます。
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Analytics でのデータ クエリ
一般的なクエリではテーブル名を最初に記述し、その後に一連の "*演算子*" を `|` で区切って続けます。
例として、直近の 3 時間のうちにアプリが受信したさまざまな国からの要求の回数を確認してみます。
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

最初にテーブル名 *requests* を記述し、必要に応じてパイプでつないだ要素を追加します。  まずは時間のフィルターを定義して、直近の3時間のレコードだけを確認するようにします。
次に国ごとのレコード数 (該当のデータは *client_CountryOrRegion* 列で確認できます) をカウントします。 最後に結果を円グラフで表示します。
<br>

![Query results](./media/app-insights-analytics/030.png)

言語には、以下のような多くの魅力的な機能があります。

* [フィルター](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)。カスタム プロパティとメトリックを含む、すべてのフィールドで未加工のアプリ テレメトリをフィルター処理します。
* [結合](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html)。複数のテーブルを結合します。要求をページ ビュー、依存関係の呼び出し、例外およびログ トレースに関連付けます。
* 強力な統計 [集計](https://docs.loganalytics.io/learn/tutorials/aggregations.html)。
* 迅速かつ強力な視覚化。
* PowerShell などのプログラムによるクエリの実行に使用できる [REST API](https://dev.applicationinsights.io/)。

[言語の完全なリファレンス](https://go.microsoft.com/fwlink/?linkid=856079)ではサポートされているすべてのコマンドを詳しく説明しており、定期的に情報を更新しています。

## <a name="next-steps"></a>次のステップ
* [Analytics ポータル](https://go.microsoft.com/fwlink/?linkid=856587)の概要
* [クエリ作成](https://go.microsoft.com/fwlink/?linkid=856078)の概要
* もっとも一般的なイディオムの変換については [SQL ユーザー用の早見表](https://aka.ms/sql-analytics)をご確認ください。
* [プレイグラウンド](https://analytics.applicationinsights.io/demo)で Analytics を試す (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。
* [紹介ビデオ](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)を見る。