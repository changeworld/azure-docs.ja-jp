---
title: " 診断クエリに関する問題のトラブルシューティング"
titleSuffix: Azure Cosmos DB
description: Azure Cosmos DB に格納されているデータのトラブルシューティングを行うために、診断ログのクエリを実行する方法について説明します
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/12/2021
ms.author: esarroyo
ms.openlocfilehash: e24ac58841c7ca786cb82e538930e66d081b1e9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045194"
---
# <a name="troubleshoot-issues-with-diagnostics-queries"></a> 診断クエリに関する問題のトラブルシューティング
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、**AzureDiagnostics (レガシ)** テーブルと **リソース固有 (プレビュー)** テーブルに送信される診断ログを使用して、Azure Cosmos DB アカウントに関する問題のトラブルシューティングに役立つ簡単なクエリを作成する方法について説明します。

Azure Diagnostics テーブルの場合、すべてのデータが 1 つのテーブルに書き込まれるので、ユーザーはクエリを実行するカテゴリを指定する必要があります。

Resource-specific (リソース別) テーブルでは、リソースの各カテゴリーに対応する個別のテーブルにデータを書き込みます (Table API では利用できません)。 これにより、データを非常に簡単に操作できるようになり、スキーマも検出しやすくなり、インジェストの待ち時間とクエリ時間の両方でパフォーマンスが向上するため、このモードをお勧めします。

## <a name="azurediagnostics-queries"></a><a id="azure-diagnostics-queries"></a> AzureDiagnostics クエリ

- 実行に 3 ミリ秒より長くかかっている操作のクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

- 操作を実行中のユーザー エージェントのクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

- 時間がかかる操作のクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
- パーティション キーの統計を取得して、データベース アカウントの上位 3 つのパーティション間のずれを評価する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

- コストの高いクエリについて要求の使用量を取得する方法

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- RU (秒単位) の多くを使用している操作を見つける方法

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

- **DataPlaneRequests** と **QueryRunTimeStatistics** のデータと結合された、消費量が 100 RU/秒を上回るすべてのクエリを取得する方法。

   ```kusto
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

- 要求の料金とクエリの実行時間を取得する方法

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```

- さまざまな操作の分布を取得する方法

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

- パーティションで消費される最大スループット量
   
   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

- パーティション キーの RU (秒単位) の消費量に関する情報を取得する方法

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 特定のパーティション キーについて要求の使用量を取得する方法

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

- 特定の期間に RU (秒単位) の多くが消費された上位のパーティション キーを取得する方法

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
   ```

- ストレージ サイズが 8 GB を超えるパーティション キーのログを取得する方法

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

- 操作、要求の課金、または応答の長さの P99 または P50 レプリケーションの待機時間を取得する方法

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s), percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s), percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s), count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
- ControlPlane ログを取得する方法
 
   必ず[キーベースのメタデータ書き込みアクセスを無効にする方法](audit-control-plane-logs.md#disable-key-based-metadata-write-access)に関する記事の説明に従ってフラグをオンに切り替えてから、Azure PowerShell、Azure CLI、または Azure Resource Manager を使用して操作を実行してください。
 
   ```kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="resource-specific-queries"></a><a id="resource-specific-queries"></a> リソース固有のクエリ

- 実行に 3 ミリ秒より長くかかっている操作のクエリを実行する方法:

    ```kusto
    CDBDataPlaneRequests 
    | where toint(DurationMs) > 3
    | summarize count() by ClientIpAddress, TimeGenerated
    ```

- 操作を実行中のユーザー エージェントのクエリを実行する方法:

   ```kusto
    CDBDataPlaneRequests
    | summarize count() by OperationName, UserAgent
   ```

- 時間がかかる操作のクエリを実行する方法:

   ```kusto
    CDBDataPlaneRequests
    | project TimeGenerated , DurationMs 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
   ```
    
- パーティション キーの統計を取得して、データベース アカウントの上位 3 つのパーティション間のずれを評価する方法:

   ```kusto
    CDBPartitionKeyStatistics
    | project RegionName, DatabaseName, CollectionName, PartitionKey, SizeKb
   ```

- コストの高いクエリについて要求の使用量を取得する方法

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 10.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- RU (秒単位) の多くを使用している操作を見つける方法

    ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize max(ResponseLength), max(RequestLength), max(RequestCharge), count = count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```

- **DataPlaneRequests** と **QueryRunTimeStatistics** のデータと結合された、消費量が 100 RU/秒を上回るすべてのクエリを取得する方法。

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 100.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- 要求の料金とクエリの実行時間を取得する方法

   ```kusto
    CDBQueryRuntimeStatistics
    | join kind= inner (
    CDBDataPlaneRequests
    ) on $left.ActivityId == $right.ActivityId
    | project DatabaseName, CollectionName, OperationName , QueryText, RequestCharge, DurationMs, bin(TimeGenerated, 1min)
   ```

- さまざまな操作の分布を取得する方法

   ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize count = count()  by OperationName, RequestResourceType, bin(TimeGenerated, 1h)
   ```

- パーティションで消費される最大スループット量

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2h) 
   | summarize max(RequestCharge) by bin(TimeGenerated, 1h), PartitionId
   ```

- パーティション キーの RU (秒単位) の消費量に関する情報を取得する方法

   ```kusto
   CDBPartitionKeyRUConsumption 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 特定のパーティション キーについて要求の使用量を取得する方法

   ```kusto
   CDBPartitionKeyRUConsumption  
   | where parse_json(PartitionKey)[0] == "2" 
   ```

- 特定の期間に RU (秒単位) の多くが消費された上位のパーティション キーを取得する方法 

   ```kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= datetime("02/12/2021, 11:20:00.000 PM") and TimeGenerated <= datetime("05/12/2021, 11:30:00.000 PM") 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey 
   | order by total desc
   ```

- ストレージ サイズが 8 GB を超えるパーティション キーのログを取得する方法

   ```kusto
   CDBPartitionKeyStatistics
   | where todouble(SizeKb) > 800000
   ``` 

- 操作、要求の課金、または応答の長さの P99 または P50 レプリケーションの待機時間を取得する方法

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(ResponseLength), 50), percentile(todouble(ResponseLength), 99), max(ResponseLength), percentile(todouble(RequestCharge), 50), percentile(todouble(RequestCharge), 99), max(RequestCharge), percentile(todouble(DurationMs), 50), percentile(todouble(DurationMs), 99), max(DurationMs),count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```
 
- ControlPlane ログを取得する方法
 
   必ず[キーベースのメタデータ書き込みアクセスを無効にする方法](audit-control-plane-logs.md#disable-key-based-metadata-write-access)に関する記事の説明に従ってフラグをオンに切り替えてから、Azure PowerShell、Azure CLI、または Azure Resource Manager を使用して操作を実行してください。
 
   ```kusto  
   CDBControlPlaneRequests
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>次のステップ 
* Cosmos DB の診断設定を作成する方法の詳細については、[診断設定の作成](cosmosdb-monitor-resource-logs.md)に関する記事を参照してください。

* Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)に関する記事を参照してください。