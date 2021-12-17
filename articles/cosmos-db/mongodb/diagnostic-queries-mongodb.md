---
title: Mongo API の高度な診断クエリに関する問題のトラブルシューティング
titleSuffix: Azure Cosmos DB
description: MongoDB API 用 Azure Cosmos DB の格納データのトラブルシューティングを行うために、診断ログのクエリを実行する方法について説明します。
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
author: StefArroyo
ms.openlocfilehash: 73dba970aea4171a99ff499300d15c63c3bbb380
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430612"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-mongodb-api"></a>MongoDB API の高度な診断クエリに関する問題のトラブルシューティング

[!INCLUDE[appliesto-all-apis-except-table](../includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL (Core) API](../cosmos-db-advanced-queries.md)
> * [MongoDB API](diagnostic-queries-mongodb.md)
> * [Cassandra API](../cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](../queries-gremlin.md)
>

この記事では、**Azure Diagnostics (レガシ)** テーブルと **リソース固有 (プレビュー)** テーブルに送信される診断ログを使用して、Azure Cosmos DB アカウントに関する問題のトラブルシューティングに役立つ、より高度なクエリを作成する方法について説明します。

Azure Diagnostics テーブルの場合、すべてのデータが 1 つのテーブルに書き込まれます。 クエリを実行するカテゴリをユーザーが指定します。 要求のフルテキスト クエリを確認したい場合は、「[Azure の診断設定を使用して Azure Cosmos DB データを監視する](../cosmosdb-monitor-resource-logs.md#full-text-query)」で、この機能を有効にする方法を参照してください。

[リソース固有テーブル](../cosmosdb-monitor-resource-logs.md#create-setting-portal)の場合、データはリソースのカテゴリごとに個別のテーブルに書き込まれます。 次の理由から、このモードをお勧めします。

- データの扱いがはるかに容易である。 
- スキーマが見つけやすい。
- インジェストの待ち時間とクエリ時間の両方でパフォーマンスが向上する。

## <a name="common-queries"></a>一般的なクエリ
一般的なクエリは、リソース固有のテーブルと Azure Diagnostics のテーブルに表示されます。

### <a name="top-n10-request-unit-ru-consuming-requests-or-queries-in-a-specific-time-frame"></a>特定の時間枠内に要求またはクエリを使用する上位 N (10) 要求ユニット (RU)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   //Enable full-text query to view entire query text
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | project PIICommandText, ActivityId, DatabaseName , CollectionName, RequestCharge
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests"
   | where TimeGenerated > ago(24h)
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s, requestCharge_s
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-or-16500-in-a-specific-time-window"></a>特定の時間枠に調整された要求 (statusCode = 429 または 16500) 

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | where ErrorCode == "429" or ErrorCode == "16500"
   | project DatabaseName, CollectionName, PIICommandText, OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests" and TimeGenerated > ago(24h)
   | where ErrorCode == "429" or ErrorCode == "16500"
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="timed-out-requests-statuscode--50-in-a-specific-time-window"></a>特定の時間枠にタイムアウトになった要求 (statusCode = 50) 

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | where ErrorCode == "50"
   | project DatabaseName, CollectionName, PIICommandText, OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests" and TimeGenerated > ago(24h)
   | where ErrorCode == "50"
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-large-response-lengths-payload-size-of-the-server-response"></a>応答の長さが長いクエリ (サーバー応答のペイロード サイズ)

# <a name="resource-specific"></a>[リソース固有](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | summarize max(ResponseLength) by PIICommandText, RequestCharge, DurationMs, OperationName, TimeGenerated
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests"
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   | summarize max(responseLength_s) by piiCommandText_s, OperationName, duration_s, requestCharge_s
   | order by max_responseLength_s desc
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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>次の手順 
* Azure Cosmos DB の診断設定を作成する方法について詳しくは、[診断設定の作成](../cosmosdb-monitor-resource-logs.md)に関するページを参照してください。
* Azure portal、Azure CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。
