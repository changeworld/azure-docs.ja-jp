---
title: Mongo API の高度な診断クエリを使用して問題のトラブルシューティングを行う
titleSuffix: Azure Cosmos DB
description: Mongo API 用 Azure Cosmos DB に格納されているデータのトラブルシューティングを行うために、診断ログのクエリを実行する方法について説明します
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
author: StefArroyo
ms.openlocfilehash: 4d5c97ce941647167e8f6d5c87b55d76e925ec87
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356691"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-mongo-api"></a>Mongo API の高度な診断クエリを使用して問題のトラブルシューティングを行う

[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL (Core) API](cosmos-db-advanced-queries.md)
> * [MongoDB API](queries-mongo.md)
> * [Cassandra API](queries-cassandra.md)
> * [Gremlin API](queries-gremlin.md)
>

この記事では、**AzureDiagnostics (レガシ)** テーブルと **リソース固有 (プレビュー)** テーブルに送信される診断ログを使用して、Azure Cosmos DB アカウントに関する問題のトラブルシューティングに役立つ、より高度なクエリを作成する方法について説明します。

Azure Diagnostics テーブルの場合、すべてのデータが 1 つのテーブルに書き込まれるので、ユーザーはクエリを実行するカテゴリを指定する必要があります。 要求のフルテキスト クエリを表示する場合は、[この記事に従って](cosmosdb-monitor-resource-logs.md#full-text-query)、この機能を有効にする方法を確認してください。

[リソース固有テーブル](cosmosdb-monitor-resource-logs.md#create-setting-portal)の場合、データはリソースのカテゴリごとに個別のテーブルに書き込まれます。 これにより、データを非常に簡単に操作できるようになり、スキーマも検出しやすくなり、インジェストの待ち時間とクエリ時間の両方でパフォーマンスが向上するため、このモードをお勧めします。

## <a name="common-queries"></a>一般的なクエリ

- 特定の時間枠内の要求またはクエリを消費する上位 N(10) 件の RU

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(1h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

- 特定の時間枠に調整された要求 (statusCode = 429 または 16500) 

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429" or StatusCode == "16500"
    | project  OperationName , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429" or statusCode_s == "16500" 
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner throttledRequests on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

- 特定の時間枠にタイムアウトになった要求 (statusCode = 50) 

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "50"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "50"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner throttledRequests on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

- 応答の長さが長いクエリ (サーバー応答のペイロード サイズ)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBMongoRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by PIICommandText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
   | order by max_responseLength_s1 desc
   ```    
---

- 物理パーティション別の RU 消費量 (レプリカ セット内のすべてのレプリカ)

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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

- 論理パーティション別の RU 消費量 (レプリカ セット内のすべてのレプリカ)

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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>次のステップ 
* Cosmos DB の診断設定を作成する方法の詳細については、[診断設定の作成](cosmosdb-monitor-resource-logs.md)に関する記事を参照してください。

* Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)に関する記事を参照してください。
