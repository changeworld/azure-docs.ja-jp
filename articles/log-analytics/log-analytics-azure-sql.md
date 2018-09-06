---
title: Log Analytics の Azure SQL Analytics ソリューション | Microsoft Docs
description: Azure SQL Analytics ソリューションは、Azure SQL データベースの管理に役立ちます
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: 47069f0af7409d87cb2d4fbbbce9dda0b1c2056e
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886562"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Azure SQL Analytics (プレビュー) を使用した Monitor Azure SQL Database の監視

![Azure SQL Analytics のシンボル](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics は、複数のエラスティック プールとサブスクリプションにわたって大規模に Azure SQL Database のパフォーマンスを監視するためのクラウド監視ソリューションです。 Azure SQL Analytics は、パフォーマンスのトラブルシューティングのために、組み込みのインテリジェンスを使用して Azure SQL Database の重要なパフォーマンス メトリックを収集し、視覚化します。 

このソリューションを使用して収集できるメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 このソリューションは、アプリケーション スタックの各層の問題を特定するのに役立ちます。 Azure 診断メトリックと Log Analytics ビューを使用して、すべての Azure SQL Database とエラスティック プールのデータを単一の Log Analytics ワークスペースに表示します。 Log Analytics では、収集、関連付けのほか、構造化データおよび非構造化データの視覚化ができます。

このソリューションは現在プレビュー段階にあり、ワークスペースごとに最大 150,000 個の Azure SQL データベースと、最大 5,000 個の SQL エラスティック プールをサポートしています。

Azure SQL Analytics ソリューションの使用に関する実践的な概要と、一般的な使用シナリオについては、埋め込みのビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>接続先ソース

Azure SQL Analytics は、Azure SQL Database とエラスティック プールの診断テレメトリのストリーミングをサポートするクラウド監視ソリューションです。 このソリューションでは、Log Analytics サービスに接続するためのエージェントが使用されないため、Windows、Linux、または SCOM のリソースとの接続はサポートされていません。次の互換性に関する表を参照してください。

| 接続先ソース | サポート | 説明 |
| --- | --- | --- |
| **[Azure 診断](log-analytics-azure-storage.md)** | **はい** | Azure のメトリックおよびログ データは、Azure によって直接 Log Analytics に送信されます。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | いいえ  | Log Analytics は、ストレージ アカウントからデータを読み取ることはしません。 |
| [Windows エージェント](log-analytics-windows-agent.md) | いいえ  | このソリューションでは、直接の Windows エージェントは使用されません。 |
| [Linux エージェント](log-analytics-linux-agents.md) | いいえ  | このソリューションでは、直接の Linux エージェントは使用されません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | いいえ  | このソリューションでは、SCOM エージェントから Log Analytics への直接接続は使用しません。 |

## <a name="configuration"></a>構成

Azure SQL Analytics ソリューションをワークスペースに追加するには、次の手順を実行します。

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) から Azure SQL Analytics ソリューションをワークスペースに追加します。
2. Azure portal で **[+ リソースの作成]** をクリックし、次に **Azure SQL Analytics** を検索します。  
    ![監視 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. 一覧から **Azure SQL Analytics (プレビュー)** を選択します
4. **[Azure SQL Analytics (プレビュー)]** 領域で、**[作成]** をクリックします。  
    ![作成](./media/log-analytics-azure-sql/portal-create.png)
5. **[新しいソリューションの作成]** 領域で、ソリューションを追加するワークスペースを新規作成するか既存のワークスペースを選択し、**[作成]** をクリックします。  
    ![ワークスペースに追加](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>診断テレメトリをストリーム配信するための Azure SQL Database とエラスティック プールの構成

Azure SQL Database およびエラスティック プールのパフォーマンスを監視するには、ワークスペースに Azure SQL Analytics ソリューションを作成した後で、診断テレメトリをソリューションにストリーム配信するように、監視対象の Azure SQL Database およびエラスティック プール リソースの**それぞれを構成**する必要があります。

- Azure SQL データベースとエラスティック プールの Azure 診断を有効にして、[Log Analytics にデータを送信するように構成](../sql-database/sql-database-metrics-diag-logging.md)します。

### <a name="to-configure-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションの構成方法

複数のサブスクリプションをサポートするためには、「[Enable Azure resource metrics logging using PowerShell (PowerShell を使用して Azure リソース メトリックのログ記録を有効にする)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)」の PowerShell スクリプトを使用します。 スクリプトを実行するときにパラメーターとしてワークスペース リソース ID を入力すると、1 つの Azure サブスクリプション内のリソースから別の Azure サブスクリプションのワークスペースに診断データを送信できます。

**例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>ソリューションの使用

ソリューションをワークスペースに追加すると、Azure SQL Analytics のタイルがワークスペースに追加され、[概要] に表示されます。 このタイルには、Azure SQL データベースと、ソリューションが接続されている Azure SQL エラスティック プールの数が表示されます。

![Azure SQL Analytics のタイル](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics データの表示

**[Azure SQL Analytics]** タイルをクリックして、Azure SQL Analytics ダッシュボードを開きます。 ダッシュボードには、さまざまなパースペクティブから監視されるすべてのデータベースの概要が含まれています。 さまざまなパースペクティブが動作するには、適切なメトリックを有効にするか、SQL リソースにログオンして、Azure Log Analytics ワークスペースにストリーミングする必要があります。

![Azure SQL Analytics の概要](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

タイルのいずれかを選択すると、特定のパースペクティブでドリルダウン レポートが開きます。 パースペクティブを選択すると、ドリル ダウン レポートが開きます。

![Azure SQL Analytics のタイムアウト](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

各パースペクティブは、サブスクリプション、サーバー、エラスティック プール、およびデータベース レベルの概要を提供します。 さらに、各パースペクティブは、右側にパースペクティブ特定のレポートを示します。 一覧からサブスクリプション、サーバー、プール、またはデータベースを選択するとドリル ダウンが続行されます。

| パースペクティブ | 説明 |
| --- | --- |
| 種類別のリソース | 監視対象のすべてのリソースをカウントするパースペクティブです。 ドリルダウンは、DTU および GB のメトリックの概要を示します。 |
| 洞察 | インテリジェントな洞察の階層型のドリルダウンを提供します。 インテリジェントな洞察の詳細を参照してください。 |
| Errors | データベースで発生した SQL エラーの階層型のドリルダウンを提供します |
| Timeouts | データベースで発生した SQL タイムアウトの階層型のドリルダウンを提供します |
| ブロッキング | データベースで発生した SQL ブロッキングの階層型のドリルダウンを提供します |
| データベース待機 | データベース レベルで発生した SQL 待機統計の階層型のドリルダウンを提供します 合計待機時間と待機の種類ごとの待機時間の概要が含まれます。 |
| クエリ実行時間 | クエリの実行時間、CPU 使用率、データ IO 使用率、ログ IO 使用率などのクエリ実行の統計の階層型のドリルダウンを提供します。 |
| クエリ待機 | 待機カテゴリ別に、クエリ待機統計の階層型のドリルダウンを提供します |

### <a name="intelligent-insights-report"></a>Intelligent Insights レポート

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) では、データベースのパフォーマンスに何が起きているかを把握できます。 収集されたすべてのインテリジェントな洞察を Insights パースペクティブを使用して視覚化およびアクセスできます。

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>エラスティック プールとデータベースのレポート

エラスティック プールとデータベースの両方に、指定された期間にリソース用に収集されたすべてのデータを表示する独自の個別のレポートがあります。

![Azure SQL Analytics データベース](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL Analytics エラスティック プール](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>クエリのレポート

クエリの実行時間とクエリの待機のパースペクティブでは、クエリ レポートを介してクエリのパフォーマンスを関連付けることができます。 このレポートは、異なるデータベース間で、クエリのパフォーマンスを比較し、選択したクエリの処理速度が速いデータベースと遅いデータベースを簡単に特定できるようになります。

![Azure SQL Analytics のクエリ](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>データの分析とアラートの作成

Azure SQL Database リソースから送られるデータを使用して簡単に[アラートを作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)できます。 ログ アラートで使用できる実用的な[ログ検索](log-analytics-log-searches.md)クエリをいくつか示します。

*高 CPU (Azure SQL Database 上)*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - 高い DTU 結果を得るために、MetricName 値の cpu_percent を dtu_consumption_percent に置き換えます。

*高 CPU (Azure SQL Database Elastic Pool 上)*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - 高い DTU 結果を得るために、MetricName 値の cpu_percent を dtu_consumption_percent に置き換えます。

*過去 1 時間の平均が 95% を超える Azure SQL Database ストレージ*

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - このクエリでは、クエリの結果が存在する (結果 > 0 である) とき、つまり条件がいくつかのデータベース上に存在するときにアラートが発生するようにアラート ルールを設定する必要があります。 出力は、定義された time_range 内で storage_threshold より上のデータベース リソースの一覧です。
> - 出力は、定義された time_range 内で storage_threshold より上のデータベース リソースの一覧です。

*Intelligent Insights に対するアラート*

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが SQLInsights 診断ログをソリューションにストリーム配信することです。
> - このクエリでは、結果の重複を回避するために、alert_run_interval と同じ頻度で実行するようにアラート ルールを設定する必要があります。 ルールは、クエリの結果が存在する (結果が > 0 である) ときにアラートが発生するように設定してください。
> - alert_run_interval をカスタマイズして、SQLInsights ログをソリューションにストリーミングするように構成したデータベースで条件が発生したかどうかをチェックする時間範囲を指定します。
> - insights_string をカスタマイズして、Insights の根本原因分析テキストの出力をキャプチャします。 これは、既存の分析情報から使用できる、ソリューションの UI に表示されるのと同じテキストです。 または、次のクエリを使用して、サブスクリプションに対して生成されるすべての分析情報のテキストを見ることもできます。 Insights に対するアラートを設定するために、クエリの出力を使用して特別な文字列を収集します。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| distinct rootCauseAnalysis_s
```

## <a name="next-steps"></a>次の手順

- Log Analytics の[ログ検索](log-analytics-log-searches.md)機能を使用して、詳細な Azure SQL データを確認します。
- Azure SQL データを表示する[独自のダッシュ ボードを作成](log-analytics-dashboards.md)します。
- Azure SQL の特定のイベントが発生した場合の[アラートを作成](log-analytics-alerts.md)します。
