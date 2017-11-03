---
title: "ワークロードの分析 - Azure SQL Data Warehouse | Microsoft Docs"
description: "Azure SQL Data Warehouse でのワークロードに対するクエリの優先順位付けを分析するための手法。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>ワークロードの分析
Azure SQL Data Warehouse でのワークロードに対するクエリの優先順位付けを分析するための手法。

## <a name="workload-groups"></a>ワークロード グループ 
SQL Data Warehouse では、ワークロード グループを使用してリソース クラスを実装します。 各 DWU サイズでのリソース クラスの動作を制御するワークロード グループは合計で 8 つあります。 いずれの DWU でも、使用するのは 8 つのワークロード グループのうち 4 つだけです。 各ワークロード グループは smallrc、mediumrc、largerc、または xlargerc のうちの 1 つに割り当てられるため、これは当然のことです。 ワークロード グループを理解するうえで、これらのワークロード グループの一部は上位の *重要度*に設定されることに注意する必要があります。 重要度は、CPU のスケジュール設定に使用されます。 重要度が "高" のクエリには、重要度が "中" のクエリと比べて 3 倍の CPU サイクルが与えられます。 そのため、同時実行スロットのマッピングにより、CPU の優先度も決まります。 16 個以上のスロットを使用するクエリは、"高" 重要度として実行されます。

次の表に、各ワークロード グループの重要度のマッピングを示します。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>同時実行スロットおよび重要度に対するワークロード グループのマッピング

| ワークロード グループ | 同時実行スロットのマッピング | MB / ディストリビューション (弾力性) | MB / ディストリビューション (コンピューティング) | 重要度のマッピング |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | 中             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | 中             |
| SloDWGroupC02   | 4                        |    400                         | 1,000                        | 中             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | 中             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | 高               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | 高               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | 高               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | 高               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | 高               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
**同時実行スロットの割り当てと使用数** の表から、DW500 では smallrc、mediumrc、largerc、および xlargerc のそれぞれで 1 個、4 個、8 個、または 16 個の同時実行スロットが使用されることが分かります。 上の表でこれらの値を調べることで、各リソース クラスの重要度を確認できます。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 での重要度に対するリソース クラスのマッピング
| リソース クラス | ワークロード グループ | 使用される同時実行スロット数 | MB / ディストリビューション | 重要度 |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | 中     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | 中     |
| largerc        | SloDWGroupC03  | 8                      | 800               | 中     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | 高       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | 中     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | 中     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | 中     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | 中     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | 高       |

## <a name="view-workload-groups"></a>ワークロード グループの表示
次の DMV クエリを使用すると、リソース ガバナーの観点からメモリ リソースの割り当ての違いを詳細に確認できます。また、トラブルシューティングを行うときにワークロード グループのアクティブな使用状況と過去の使用状況を分析することもできます。

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>キューに配置されたクエリの検出とその他の DMV
`sys.dm_pdw_exec_requests` DMV を使用すると、同時実行キューで待機中のクエリを特定できます。 同時実行スロットを待機しているクエリは、 **中断**状態となります。

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

ワークロード管理ロールを確認するには、 `sys.database_principals`を使用します。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

次のクエリは、各ユーザーが割り当てられているロールを示します。

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse には、次の待機の種類があります。

* **LocalQueriesConcurrencyResourceType**: 同時実行スロットのフレームワークの外に配置されたクエリ。 DMV クエリと、 `SELECT @@VERSION` のようなシステム関数は、ローカル クエリの例です。
* **UserConcurrencyResourceType**: 同時実行スロットのフレームワーク内に配置されたクエリ。 エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。
* **DmsConcurrencyResourceType**: データ移動操作に起因する待機。
* **BackupConcurrencyResourceType**: この待機は、データベースがバックアップ中であることを示します。 この種類のリソースの最大値は 1 です。 同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。

`sys.dm_pdw_waits` DMV を使用すると、要求がどのリソースを待っているのかを調べることができます。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV では、特定のクエリによって使用されるリソースの待機のみが表示されます。 リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されます。これに対し、シグナルの待機時間は、基になる SQL Server によりクエリが CPU にスケジュール設定されるまでの時間です。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
`sys.dm_pdw_resource_waits` DMV を使用して、許可された同時実行スロットの数を計算することもできます。

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV を使用すると、待機のこれまでの傾向を分析できます。

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>次のステップ
データベース ユーザーの管理とセキュリティの詳細については、「[SQL Data Warehouse でのデータベース保護][Secure a database in SQL Data Warehouse]」を参照してください。 大規模なリソース クラスを使用して、クラスター化された列ストア インデックスの品質を向上させる方法については、「 [セグメントの品質を向上させるためのインデックスの再構築]」を参照してください。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[セグメントの品質を向上させるためのインデックスの再構築]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
