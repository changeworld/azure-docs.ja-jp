---
title: 問題を高度な診断クエリでトラブルシューティングする (SQL API)
titleSuffix: Azure Cosmos DB
description: Azure Cosmos DB に格納されているデータをトラブルシューティングするために、診断ログに対してクエリを実行する方法について説明します - SQL API。
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: 16c4fe809fc411a9d6eec89ed7ceeb04dce317d2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097775"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-sql-core-api"></a>SQL (Core) API の問題を高度な診断クエリでトラブルシューティングする

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SQL (Core) API](cosmos-db-advanced-queries.md)
> * [MongoDB API](mongodb/diagnostic-queries-mongodb.md)
> * [Cassandra API](cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](graph/diagnostic-queries-gremlin.md)
>

この記事では、**Azure Diagnostics (レガシ)** テーブルと **リソース固有 (プレビュー)** テーブルに送信される診断ログを使用して、Azure Cosmos DB アカウントに関する問題のトラブルシューティングに役立つ、より高度なクエリを作成する方法について説明します。

Azure Diagnostics テーブルの場合、すべてのデータが 1 つのテーブルに書き込まれます。 クエリを実行するカテゴリをユーザーが指定します。 要求のフルテキスト クエリを確認したい場合は、「[Azure の診断設定を使用して Azure Cosmos DB データを監視する](cosmosdb-monitor-resource-logs.md#full-text-query)」で、この機能を有効にする方法を参照してください。

[リソース固有テーブル](cosmosdb-monitor-resource-logs.md#create-setting-portal)の場合、データはリソースのカテゴリごとに個別のテーブルに書き込まれます。 次の理由から、このモードをお勧めします。

- データの扱いがはるかに容易である。 
- スキーマが見つけやすい。
- インジェストの待ち時間とクエリ時間の両方でパフォーマンスが向上する。

## <a name="common-queries"></a>一般的なクエリ
一般的なクエリは、リソース固有のテーブルと Azure Diagnostics のテーブルに表示されます。

### <a name="top-n10-queries-ordered-by-request-unit-ru-consumption-in-a-specific-time-frame"></a>要求ユニット (RU) の消費量順に並べられた特定の時間枠の上位 N(10) のクエリ

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , QueryText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(24h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , querytext_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-in-a-specific-time-window"></a>特定の時間枠に調整された要求 (statusCode = 429) 

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , QueryText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests" and statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , querytext_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-the-largest-response-lengths-payload-size-of-the-server-response"></a>応答時間が最も長いクエリ (サーバー応答のペイロード サイズ)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBQueryRuntimeStatistics
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by QueryText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by querytext_s
   | order by max_responseLength_s1 desc
   ```    
---

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>物理パーティション別の RU 消費量 (レプリカ セット内のすべてのレプリカ)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>論理パーティション別の RU 消費量 (レプリカ セット内のすべてのレプリカ)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB の診断設定を作成する方法について詳しくは、[診断設定の作成](cosmosdb-monitor-resource-logs.md)に関するページを参照してください。
* Azure portal、Azure CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。
