---
title: Azure Cosmos DB の監視 | Microsoft Docs
description: Azure Cosmos DB のパフォーマンスと可用性を監視する方法について説明します。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250363"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB の監視
Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Cosmos データベースによって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Cosmos DB では、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データを作成します。Azure Monitor は Azure のフルスタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。 

Azure サービスの監視について理解が十分でない場合は、まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/insights/monitor-azure-resource.md)」の記事にある次の事項の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事を基に、Azure Cosmos DB から収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor for Cosmos DB (プレビュー)
[Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) は、[Azure Monitor のブック機能](../azure-monitor/app/usage-workbooks.md)に基づいており、以下のセクションで説明する、Cosmos DB 用に収集される監視データと同じものを使用します。 このツールを使用して、すべての Azure Cosmos DB リソースの全体的なパフォーマンス、障害、容量、および運用の正常性を、一元的な対話型エクスペリエンスで把握できます。また、Azure Monitor の他の機能を利用して詳細な分析とアラート生成を実行できます。 

![Azure Monitor for Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の操作レベルのメトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインする

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   ![Azure Monitor のメトリック ウィンドウ](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な**サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   ![メトリックを表示する Cosmos DB アカウントの選択](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[要求ユニット]** および集計値として **[Avg]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   ![Azure portal からのメトリックの選択](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>メトリックにフィルターを追加する

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 メトリックにフィルターを適用するには、 **[フィルターの追加]** を選択し、**OperationType** などの必要なプロパティを選択し、**Query** などの値を選択します。 その後グラフには、選択した期間のクエリ操作で消費された要求ユニットが表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

![メトリック細分性を選択するためのフィルターの追加](./media/monitor-cosmos-db/add-metrics-filter.png)

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 たとえば、次の図に示すように、要求ユニットを操作の種類ごとにグループ化し、すべての操作のグラフを一度に表示できます。

![分割の適用フィルターの追加](./media/monitor-cosmos-db/apply-metrics-splitting.png)

特定のデータベース、コンテナー、または操作に対するサーバー側の待機時間のメトリックを表示するもう 1 つの例を次に示します。

![サーバー側の待機時間のメトリック](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Azure Cosmos DB から収集したデータの監視

Azure Cosmos DB は、他の Azure リソースと同じ種類の監視データを収集します。これについては、[Azure リソースの監視データ](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)に関する記事を参照してください。 Azure Cosmos DB によって作成されるログおよびメトリックの詳細なリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。

Azure portal で、各 Azure Cosmos データベースの **[概要]** ページでは、データベースの要求や時間ごとの課金使用量など、データベースの使用状況の要約を表示します。 これは有用な情報ですが、見ることのできる監視データはごくわずかです。 このデータの一部は自動的に収集され、データベースを作成するとすぐに分析に使用できるようになりますが、一部の構成では追加のデータ収集を有効にすることができます。

![[概要] ページ](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>メトリック データの分析

Azure Cosmos DB は、メトリックを操作するためのカスタム エクスペリエンスを提供します。 このエクスペリエンスの使用と、さまざまな Azure Cosmos DB シナリオの分析の詳細については、[Azure Monitor からの Azure Cosmos DB メトリックの監視とデバッグ](cosmos-db-azure-monitor-metrics.md)に関するページを参照してください。

**Azure Monitor** のメニューから **[Metrics]\(メトリック\)** を開き、メトリックス エクスプローラーを使用して、Azure Cosmos DB のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/platform/metrics-getting-started.md)」を参照してください。 Azure Cosmos DB のすべてのメトリックは、**Cosmos DB standard metrics** (Cosmos DB 標準メトリック) 名前空間に含まれています。 フィルターをグラフに追加するときは、次のディメンションをこれらのメトリックと共に使用できます。

- CollectionName
- DatabaseName
- OperationType
- リージョン
- StatusCode


## <a name="analyzing-log-data"></a>ログ データの分析
Azure Monitor のログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 Azure Cosmos DB は、次のテーブルにデータを格納します。

| テーブル | 説明 |
|:---|:---|
| AzureDiagnostics | リソース ログを格納するために複数のサービスによって使用される共通テーブル。 Azure Cosmos DB のリソース ログは `MICROSOFT.DOCUMENTDB` で識別できます。   |
| AzureActivity    | アクティビティ ログのすべてのレコードを格納する共通テーブル。 


> [!IMPORTANT]
> Azure Cosmos DB のメニューから **[ログ]** を選択すると、クエリのスコープが現在の Azure Cosmos データベースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のデータベースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/log-query/scope.md)」を参照してください。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Monitor での Azure Cosmos DB Log Analytics のクエリ

**[ログ検索]** 検索バーに入力して Azure Cosmos コンテナーの監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](../log-analytics/log-analytics-log-search-upgrade.md)で使用できます。

次に、Azure Cosmos データベースの監視に使用できるクエリを示します。

* 指定した期間の Azure Cosmos DB のすべての診断ログをクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* ログに記録された最新の 10 件のイベントをクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* 操作の種類でグループ分けされたすべての操作をクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* リソースでグループ分けされたすべての操作をクエリするには:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* リソースでグループ分けされたすべてのユーザー アクティビティをクエリするには:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* **DataPlaneRequests** と **QueryRunTimeStatistics** のデータと結合された 100 RU より大きいすべてのクエリを取得するには:

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* 実行時間が 3 ミリ秒を超えている操作をクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 操作を実行しているエージェントをクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* 実行時間が長い操作が実行された時刻をクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* パーティション キーの統計を取得して、データベース アカウントの上位 3 つのパーティション間のずれを評価するには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB をプログラムで監視する
ポータルで使用できるアカウント レベルのメトリック (アカウント ストレージの使用状況、要求総数) は、SQL API で使用することはできません。 ただし、SQL API を使用してコレクション レベルで使用状況データを取得できます。 コレクション レベルのデータを取得するには、次の操作を行います。

* REST API を使用するには、 [コレクションに対して GET を実行](https://msdn.microsoft.com/library/mt489073.aspx)します。 コレクションのクォータおよび使用状況の情報が、応答の x-ms-resource-quota および x-ms-resource-usage ヘッダーに返されます。
* .NET SDK を使用するには、[DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) メソッドを使用します。これは、[CollectionSizeUsage](https://msdn.microsoft.com/library/dn799209.aspx)、**DatabaseUsage**、**DocumentUsage** などの多数の使用状況プロパティを含む **ResourceResponse** を返します。

その他のメトリックにアクセスするには、 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)を使用します。 使用できるメトリック定義は、次の URL を呼び出すことで取得できます。

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

クエリでメトリックを個別に取得する場合には、次の形式を使用します。

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB によって作成されるログおよびメトリックのリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/insights/monitor-azure-resource.md)」を参照してください。
