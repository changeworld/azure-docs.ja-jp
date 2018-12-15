---
title: Azure Monitor での Log Analytics データの分析 | Microsoft Docs
description: Log Analytics からデータを取得するにはログ検索が必要です。  この記事では、Log Analytics で新しいログ検索を使用する方法と、ログ検索を作成する前に理解しておく必要がある概念について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: bc37b3b60a5ad7f4e2b4794e4fcb74c1a5004b75
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336885"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Azure Monitor で Log Analytics データを分析する

Azure Monitor で収集されたログ データは、[Azure データ エクスプローラー](/azure/data-explorer)に基づく Log Analytics ワークスペースに保存されます。 さまざまなソースからテレメトリが収集され、[データ エクスプローラーのクエリ言語](/azure/kusto/query)を使用してデータが取得および分析されます。

> [!NOTE]
> Log Analytics は以前、Azure 内の独自のサービスとして扱われていました。 そのサービスは Azure Monitor の一部と見なされるようになり、ログ データの格納とクエリ言語によるログ データの分析に重点が置かれています。 データ収集用の Windows および Linux のエージェント、既存のデータを視覚化するビュー、問題を事前に通知するアラートなど、Log Analytics の一部と見なされていた機能は変更されていませんが、Azure Monitor の一部と見なされるようになりました。



## <a name="log-queries"></a>ログ クエリ

Log Analytics から任意のデータを取得するにはログ クエリが必要です。  [ポータルでデータを分析する](../../azure-monitor/log-query/portals.md)、特定の条件の通知を受け取る[警告ルールを構成する](../../azure-monitor/platform/alerts-metric.md)、[Log Analytics API を使用してデータを取得する](https://dev.loganalytics.io/)といった場合はいずれも、クエリを使用して必要なデータを指定します。  この記事では、Log Analytics 内でログ クエリを使用する方法と、ログ クエリを作成する前に理解しておく必要がある概念について説明します。



## <a name="where-log-queries-are-used"></a>ログ クエリを使用する場所

Log Analytics のクエリは、次に示すさまざまな方法で使用します。

- **ポータル。** [Azure portal](../../azure-monitor/log-query/portals.md) では、ログ データの対話型分析を行うことができます。  これにより、クエリを編集し、さまざまな形式および視覚化で結果を分析することができます。  
- **警告ルール。** [警告ルール](../../azure-monitor/platform/alerts-overview.md)は、ワークスペースのデータの問題を事前に特定します。  各警告ルールは、定期的に自動実行されるログ検索に基づいてます。  結果を検査することで、警告を作成するかどうかが決まります。
- **ダッシュボード。** クエリの結果は [Azure ダッシュボード](../../azure-monitor/platform/dashboards.md)にピン留めすることができます。これにより、ログ データとメトリック データをまとめて視覚化し、必要に応じて、他の Azure ユーザーと共有することができます。 
- **ビュー。**  ユーザー ダッシュボードに含めるデータの視覚化を作成するには、[ビュー デザイナー](../../azure-monitor/platform/view-designer.md)を使用します。  ログ クエリによって提供されるのは、各ビューの[タイル](../../azure-monitor/platform/view-designer-tiles.md)および[視覚化パーツ](../../azure-monitor/platform/view-designer-parts.md)で使用するデータです。  
- **エクスポート。**  Log Analytics ワークスペースから Excel または [Power BI](../../azure-monitor/platform/powerbi.md) にデータをエクスポートする場合は、ログ クエリを作成して、エクスポートするデータを定義します。
- **PowerShell。** [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) を使用する Azure Automation Runbook またはコマンド ラインから PowerShell スクリプトを実行して、Log Analytics からデータを取得できます。  このコマンドレットでは、クエリによって、取得するデータを決定する必要があります。
- **Log Analytics API。**  [Log Analytics のログ検索 API](../../azure-monitor/platform/alerts-overview.md) を使用すると、任意の REST API クライアントによってワークスペースからログ データを取得することができます。  API 要求には Log Analytics に対して実行するクエリが含まれており、これにより取得するデータを決定します。

![ログ検索](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>クエリを記述する
Log Analytics では、[データ エクスプローラーのクエリ言語のバージョン](../../azure-monitor/log-query/get-started-queries.md)を使用して、さまざまな方法でログ データを取得および分析します。  通常、基本的なクエリから開始し、要件の複雑化に合わせて、より高度な機能の使用へと進んでいきます。

クエリの基本構造では、ソース テーブルの後に、一連の演算子をパイプ文字 `|` で区切って記述します。  複数の演算子を連結してデータを絞り込み、高度な機能を実行できます。

たとえば、過去 1 日で、エラー イベントが多い上位 10 台のコンピューターを確認する必要があるとします。

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

また、過去 1 日に、ハートビートがなかったコンピューターを確認したい場合もあります。

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

先週の各コンピューターにおけるプロセッサ使用率を表す折れ線グラフはどうでしょうか。

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

この簡単な例からわかるように、操作しているデータの種類に関係なく、クエリの構造は似ています。  これは個別のステップに分割できます。このステップで 1 つのコマンドの結果として生成されるデータが、パイプラインを介して次のコマンドに送信されます。

サブスクリプション内の Log Analytics ワークスペース全体でデータをクエリすることもできます。

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics データの編成方法
クエリを作成する場合は、最初に、どのテーブルに目的のデータが含まれるかを確認します。 種類が異なるデータは、各 [Log Analytics ワークスペース](../../azure-monitor/learn/quick-create-workspace.md)内で専用のテーブルにそれぞれ分けられます。  各種データ ソースのドキュメントには、作成されたデータ型の名前と、各プロパティの説明が含まれています。  多くのクエリでは、1 つのテーブルのデータのみが必要ですが、複数のテーブルのデータを含めるためにさまざまなオプションを使用するクエリもあります。

要求、例外、トレース、および Log Analytics での使用状況などのアプリケーション データは [Application Insights](../../application-insights/app-insights-overview.md) によって格納されますが、このデータはその他のログ データとは別のパーティションに格納されます。 このデータにアクセスするには、同じクエリ言語を使用します。ただし、アクセスするには [Application Insights コンソール](../../application-insights/app-insights-analytics.md)または [Application Insights REST API](https://dev.applicationinsights.io/) を使用する必要があります。 [クロスリソース クエリ](../../azure-monitor/log-query/cross-workspace-query.md)を使用すれば、Application Insights のデータを Log Analytics 内の他のデータと結合することができます。


![テーブル](media/log-query-overview/queries-tables.png)







## <a name="next-steps"></a>次の手順

- [ログ検索の作成および編集に使用するポータル](../../azure-monitor/log-query/portals.md)について学習します。
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](../../azure-monitor/log-query/get-started-queries.md)を確認します。
