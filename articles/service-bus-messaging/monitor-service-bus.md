---
title: Azure Service Bus の監視
description: Azure Monitor を使用して Azure Service Bus のメトリックを表示、分析し、そのアラートを作成する方法について説明します。
ms.topic: conceptual
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: 90930a5f76fd1aaccd7968febdf7dbdbd684ab28
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110192133"
---
# <a name="monitor-azure-service-bus"></a>Azure Service Bus を監視する
Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Service Bus によって生成される監視データと、Azure Monitor でこのデータを分析してアラートを生成する方法について説明します。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Service Bus は、Azure のフルスタック監視サービスである [Azure Monitor](../azure-monitor/overview.md) を使用して監視データを作成します。 Azure Monitor には、Azure リソースを監視するための完全な機能セットが用意されています。 また、他のクラウドやオンプレミスのリソースも監視できます。

まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Service Bus のために収集された特定のデータについて説明します。 これらのセクションでは、Azure ツールを使用してデータ収集を構成し、このデータを分析する例も紹介します。

> [!TIP]
> Azure Monitor に関連したコストを把握するには、[使用量と推定コスト](../azure-monitor//usage-estimated-costs.md)に関する記事を参照してください。 データが Azure Monitor に表示されるまでにかかる時間を把握するには、[ログ データのインジェスト時間](../azure-monitor/logs/data-ingestion-time.md)に関する記事を参照してください。

## <a name="monitoring-data-from-azure-service-bus"></a>Azure Service Bus の監視データ
Azure Service Bus では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure Service Bus によって作成されるログおよびメトリックの詳細なリファレンスについては、「[Azure Service Bus 監視データのリファレンス](monitor-service-bus-reference.md)」を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング
プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Service Bus のカテゴリは、「[Azure Service Bus 監視データのリファレンス](monitor-service-bus-reference.md#resource-logs)」に一覧表示されています。

### <a name="azure-storage"></a>Azure Storage 
診断ログ情報は、**insights-logs-operationlogs** および **insights-metrics-pt1m** という名前のコンテナーに格納されています。

操作ログのサンプル URL: `https://<Azure Storage account>.blob.core.windows.net/insights-logs-operationallogs/resourceId=/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<Namespace name>/y=<YEAR>/m=<MONTH-NUMBER>/d=<DAY-NUMBER>/h=<HOUR>/m=<MINUTE>/PT1H.json`。 メトリック ログの URL は似ています。 

### <a name="azure-event-hubs"></a>Azure Event Hubs
診断ログ情報は、**insights-logs-operationlogs** および **insights-metrics-pt1m** という名前のイベント ハブに格納されています。 独自のイベント ハブを選択することもできます。 

### <a name="log-analytics"></a>Log Analytics 
診断ログ情報は、**AzureDiagnostics** および **AzureMetrics** という名前のテーブルに格納されます。 

### <a name="sample-operational-log-output-formatted"></a>操作ログのサンプル出力 (書式設定済み)

```json
{
    "Environment": "PROD",
    "Region": "East US",
    "ScaleUnit": "PROD-BL2-002",
    "ActivityId": "a097a88a-33e5-4c9c-9c64-20f506ec1375",
    "EventName": "Retrieve Namespace",
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "SubscriptionId": "<Azure subscription ID>",
    "EventTimeString": "5/18/2021 3:25:55 AM +00:00",
    "EventProperties": "{\"SubscriptionId\":\"<Azure subscription ID>\",\"Namespace\":\"spsbus0213ns\",\"Via\":\"https://spsbus0213ns.servicebus.windows.net/$Resources/topics?api-version=2017-04&$skip=0&$top=100\",\"TrackingId\":\"a097a88a-33e5-4c9c-9c64-20f506ec1375_M8CH3_M8CH3_G8\"}",
    "Status": "Succeeded",
    "Caller": "rpfrontdoor",
    "category": "OperationalLogs"
}
```

### <a name="sample-metric-log-output-formatted"></a>メトリック ログのサンプル出力 (書式設定済み)

```json
{
    "count": 1,
    "total": 4,
    "minimum": 4,
    "maximum": 4,
    "average": 4,
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "time": "2021-05-18T03:27:00.0000000Z",
    "metricName": "IncomingMessages",
    "timeGrain": "PT1M"
}
```

> [!IMPORTANT]
> これらの設定を有効にするには、追加の Azure サービス (ストレージ アカウント、イベント ハブ、または Log Analytics) が必要であり、コストが増加する可能性があります。 推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)にアクセスしてください。

> [!NOTE]
> 診断設定でメトリックを有効にするとき、現在、ディメンション情報は、ストレージ アカウント、イベント ハブ、または Log Analytics に送信される情報の一部として含まれません。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析
Azure Service Bus のメトリックと他の Azure サービスのメトリックを分析するには、Service Bus 名前空間のホーム ページ上で **[Azure Monitor]** セクションから **[メトリック]** を選択します。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 収集されるプラットフォーム メトリックの一覧については、「[Azure Service Bus 監視データのリファレンス メトリック](monitor-service-bus-reference.md#metrics)」を参照してください。

![Service Bus 名前空間が選択されたメトリックス エクスプローラー](./media/monitor-service-bus/metrics.png)

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

> [!TIP]
> Azure Monitor のメトリック データは 90 日分利用できます。 ただし、グラフを作成するときは、30 日分だけ視覚化できます。 たとえば、90 日間の期間を視覚化する場合は、90 日間の期間内で 30 日間の 3 つのグラフに分割する必要があります。

### <a name="filtering-and-splitting"></a>フィルター処理と分割
ディメンションをサポートするメトリックについては、ディメンション値を使用してフィルターを適用できます。 たとえば、フィルターを追加して `EntityName` をキューまたはトピックの名前に設定します。 また、メトリックをディメンションで分割して、メトリックのセグメントを互いに比較してどのように異なるかを視覚化することもできます。 フィルター処理と分割の詳細については、[Azure Monitor の高度な機能](../azure-monitor/essentials/metrics-charts.md)に関する記事をご覧ください。

## <a name="analyzing-logs"></a>ログの分析
Azure Monitor Log Analytics を使用するには、診断構成を作成して、__Log Analytics への情報の送信__ を有効にする必要があります。 詳細については、「[コレクションとルーティング](#collection-and-routing)」セクションを参照してください。 Azure Monitor のログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 Azure Service Bus によって、データが **AzureDiagnostics** テーブルと **AzureMetrics** テーブルに格納されます。

> [!IMPORTANT]
> Azure Service Bus のメニューから **[ログ]** を選択すると、クエリのスコープが現在のワークスペースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のデータベースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/logs/scope.md)」を参照してください。

ログおよびメトリックの詳細なリファレンスについては、「[Azure Service Bus 監視データのリファレンス](monitor-service-bus-reference.md)」を参照してください。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> Azure Service Bus のメニューから **[ログ]** を選択すると、クエリのスコープが現在の Azure Service Bus 名前空間に設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のワークスペースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/logs/scope.md)」を参照してください。

次に、Azure Service Bus リソースの監視に使用できるサンプル クエリを示します。 

+ 過去 7 日間の管理操作を取得します。 

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | summarize count() by EventName_s, _ResourceId
    ```

+ "キーが見つかりません" というエラーを発生させた、キー コンテナーへのアクセス試行を取得します。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS" 
    | where Category == "Error" and OperationName == "wrapkey"
    | project Message, _ResourceId
    ```

+ 過去 7 日間のエラーを取得します

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "Error" 
    | summarize count() by EventName_s, _ResourceId
    ```

+ キー コンテナーで実行された操作を取得して、キーを無効にするか復元します。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where (Category == "info" and (OperationName == "disable" or OperationName == "restore"))
    | project Message, _ResourceId
    ```

+ 自動的に削除されたすべてのエンティティを取得します

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | where EventName_s startswith "AutoDelete"
    | summarize count() by EventName_s, _ResourceId    
    ```
    
## <a name="alerts"></a>警告
Azure Service Bus のアラートにアクセスするには、Service Bus 名前空間のホームページ上で **[Azure Monitor]** セクションから **[アラート]** を選択します。 アラートの作成の詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)」を参照してください。


## <a name="next-steps"></a>次の手順

- ログおよびメトリックのリファレンスについては、「[Azure Service Bus 監視データのリファレンス](monitor-service-bus-reference.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
