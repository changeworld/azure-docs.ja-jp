---
title: メモリの問題のトラブルシューティング
titleSuffix: Azure SQL Database
description: Azure SQL Database のメモリ不足の問題をトラブルシューティングする手順について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 11/03/2021
ms.openlocfilehash: 07ff08303cc68934d5d7191952536b9101e12cf6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494712"
---
# <a name="troubleshoot-out-of-memory-errors-with-azure-sql-database"></a>Azure SQL Database でのメモリ不足エラーのトラブルシューティング  
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL データベースエンジンがクエリを実行するのに十分なメモリを割り当てられなかった場合に、エラー メッセージが表示されることがあります。 これは、選択されたサービス目標の制限、ワークロードに必要とされるメモリの集計、クエリに必要とされるメモリなど、さまざまな理由が原因となります。 Azure SQL Database のメモリ リソースの制限の詳細については、「[Azure SQL Database でのリソース管理](resource-limits-logical-server.md#memory)」をご覧ください。

> [!NOTE]
> **この記事では、Azure SQL Database について重点的に説明します。** SQL Server でのメモリ不足の問題のトラブルシューティングの詳細については、「[MSSQLSERVER_701](/sql/relational-databases/errors-events/mssqlserver-701-database-engine-error)」をご覧ください。

次のエラーの対応として、続く調査方法を試してください。

- エラー コード 701 とエラー メッセージ「リソース プール '% ls ' のシステム メモリが不足しているため、このクエリを実行できません」が表示されます。
- エラー コード 802 とエラー メッセージ「バッファー プールで使用可能なメモリが不足しています」が表示されます。

## <a name="investigate-memory-allocation"></a>メモリ割り当てを調査する

Azure SQL Database でメモリ不足エラーが解消されない場合は、Azure portal 内のデータベースのサービス レベル目標を少なくとも一時的に増やすことを検討してください。 メモリ不足エラーが解消されない場合は、次のクエリを使用して、メモリ不足の状態に影響する可能性がある、異常に高いクエリのメモリ許可を探します。 エラーが発生したデータベース (Azure SQL 論理サーバーの `master` データベースではない) で、次のクエリの例を実行します。  

### <a name="use-dmvs-to-view-memory-clerks"></a>DMV (動的管理ビュー) を使用してメモリ クラークを表示する

メモリ不足エラーが最近になって発生した場合は、メモリ クラークへのメモリ割り当てを表示することによって、広範な調査を開始します。 メモリ クラークは、この Azure SQL Database のデータベース エンジンの内部にあります。 割り当てられたページの観点での上位のメモリ クラークは、最も多くのメモリを消費しているのは SQL Server のどの種類のクエリまたは機能であるかを示す場合があります。 


```sql
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY pages_kb DESC;
GO
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY virtual_memory_committed_kb DESC;
```

 - MEMORYCLERK_SQLQERESERVATIONS など、いくつかの一般的なメモリ クラークは、大規模なメモリ許可を持つクエリを識別し、インデックス作成とインデックスのチューニングを強化してパフォーマンスを向上させることによって解決するのが最善です。 
 - OBJECTSTORE_LOCK_MANAGER は、メモリ許可に関連付けられていませんが、無効にされたロックのエスカレーションや非常に大きなトランザクションなどによってクエリが多数のロックを要求した場合は、高くなると予想されます。 
 - 一部のクラークは最も使用率が高いと想定されています。 MEMORYCLERK_SQLBUFFERPOOL はほぼ常に最上位のクラークであり、列ストア インデックスが使用されている場合は CACHESTORE_COLUMNSTOREOBJECTPOOL が高くなります。 これらのクラークによって、使用率が最も高いことが予想されます。 

 メモリ クラークの詳細については、[sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql) をご覧ください。 

### <a name="use-dmvs-to-investigate-active-queries"></a>DMV (動的管理ビュー) を使用してアクティブなクエリを調査する 

ほとんどの場合、失敗したクエリは、このエラーの原因ではありません。 

次の Azure SQL Database のサンプル クエリは、メモリ許可を現在保持しているか待機しているトランザクションに関する重要な情報を返します。 調査とパフォーマンス チューニングのために特定した最上位のクエリを対象にして、それらが意図したとおりに実行されているかどうかを評価します。 メモリ集中型レポート クエリまたはメンテナンス操作のタイミングを検討します。

```sql
--Active requests with memory grants
SELECT
--Session data 
  s.[session_id], s.open_transaction_count
--Memory usage
, r.granted_query_memory, mg.grant_time, mg.requested_memory_kb, mg.granted_memory_kb, mg.required_memory_kb, mg.used_memory_kb, mg.max_used_memory_kb     
--Query 
, query_text = t.text, input_buffer = ib.event_info, query_plan_xml = qp.query_plan, request_row_count = r.row_count, session_row_count = s.row_count
--Session history and status
, s.last_request_start_time, s.last_request_end_time, s.reads, s.writes, s.logical_reads, session_status = s.[status], request_status = r.status
--Session connection information
, s.host_name, s.program_name, s.login_name, s.client_interface_name, s.is_user_process
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests AS r 
    ON r.[session_id] = s.[session_id]
LEFT OUTER JOIN sys.dm_exec_query_memory_grants AS mg 
    ON mg.[session_id] = s.[session_id]
OUTER APPLY sys.dm_exec_sql_text (r.[sql_handle]) AS t
OUTER APPLY sys.dm_exec_input_buffer(s.[session_id], NULL) AS ib 
OUTER APPLY sys.dm_exec_query_plan (r.[plan_handle]) AS qp 
WHERE mg.granted_memory_kb > 0
ORDER BY mg.granted_memory_kb desc, mg.requested_memory_kb desc;
```

大きなメモリ許可を保持しているか待機している現在実行中のクエリを停止するために、KILL ステートメントを使用することを検討できます。 このステートメントは、特にビジネス クリティカルなプロセスが実行されている場合は慎重に使用してください。 詳しくは、「[KILL &#40;Transact-SQL&#41;](/sql/t-sql/language-elements/kill-transact-sql)」をご覧ください。 


### <a name="use-query-store-to-investigate-past-query-memory-usage"></a>クエリ ストアを使用して過去のクエリ メモリ使用量を調査する

前のサンプル クエリでは、ライブ クエリの結果のみがレポートされますが、次のクエリでは、[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して過去のクエリ実行に関する情報を返します。 これは、過去に発生したメモリ不足エラーの調査に役立ちます。

次の Azure SQL Database のサンプル クエリは、クエリ ストアによって記録されたクエリの実行に関する重要な情報を返します。 調査とパフォーマンス チューニングのために特定した最上位のクエリを対象にして、それらが意図したとおりに実行されているかどうかを評価します。 `qsp.last_execution_time` に時間フィルターを適用して、結果を最近の履歴に制限します。 TOP 句を調整して、環境に応じて結果を増減することができます。

```sql
SELECT TOP 10 PERCENT --limit results
  a.plan_id, query_id, plan_group_id, query_sql_text
, query_plan = TRY_CAST(query_plan as XML)
, avg_query_max_used_memory
, min_query_max_used_memory
, max_query_max_used_memory
, last_query_max_used_memory 
, last_execution_time  
, query_count_executions
    FROM (
    SELECT 
      qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    , last_execution_time = MAX(qsp.last_execution_time)
    , query_count_executions = SUM(qsrs.count_executions) 
    , avg_query_max_used_memory = AVG(qsrs.avg_query_max_used_memory)
    , min_query_max_used_memory = MIN(qsrs.min_query_max_used_memory)
    , max_query_max_used_memory = MAX(qsrs.max_query_max_used_memory)
    , last_query_max_used_memory = MAX(qsrs_latest.last_query_max_used_memory) --only from latest result
    FROM sys.query_store_plan AS qsp 
    INNER JOIN sys.query_store_query AS qsq
        ON qsp.query_id = qsq.query_id
    INNER JOIN sys.query_store_query_text AS qsqt
        ON qsq.query_text_id = qsqt.query_text_id 
    INNER JOIN sys.query_store_runtime_stats AS qsrs
        ON qsp.plan_id = qsrs.plan_id 
    INNER JOIN (SELECT plan_id
            , last_query_max_used_memory 
            , rownum = ROW_NUMBER() OVER (PARTITION BY plan_id ORDER BY last_execution_time DESC)
            FROM sys.query_store_runtime_stats qsrs) AS qsrs_latest
        ON qsrs_latest.plan_id = qsp.plan_id
        AND qsrs_latest.rownum = 1 --use latest last_query_max_used_memory per plan_id
    WHERE DATEADD(hour, -24, sysdatetime()) < qsp.last_execution_time --past 24 hours only
    AND qsrs_latest.last_query_max_used_memory > 0
    GROUP BY qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    ) AS a
ORDER BY max_query_max_used_memory DESC, avg_query_max_used_memory DESC;
```

### <a name="extended-events"></a>拡張イベント
前の情報に加えて、サーバー上のアクティビティのトレースをキャプチャすると、Azure SQL Database のメモリ不足の問題を十分に調査するのに役立つ場合があります。 

SQL Server でトレースをキャプチャする方法は 2 つあります。拡張イベント (Xevent) とプロファイラー トレースです。 ただし、[SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) は、Azure SQL Database でサポートされていない非推奨のトレース テクノロジです。 [拡張イベント](/sql/relational-databases/extended-events/extended-events)は、より汎用性が高く、監視対象のシステムへの影響が少ない新しいトレース テクノロジであり、そのインターフェイスは SQL Server Management Studio (SSMS) に統合されています。 

SSMS の[拡張イベントの新しいセッション ウィザード](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)の使用方法について説明しているドキュメントを参照してください。 ただし、Azure SQL Database の場合、SSMS によって、オブジェクト エクスプローラーの各データベースの下に拡張イベント サブフォルダーが提供されます。 拡張イベント セッションを使用して、これらの有用なイベントをキャプチャし、それらを生成するクエリを特定します。 

-   カテゴリ エラー:
    - error_reported
    - exchange_spill
    - hash_spill_details
    
-   カテゴリ実行:
    - excessive_non_grant_memory_used

-   カテゴリ メモリ:
    - query_memory_grant_blocking
    - query_memory_grant_usage

メモリ許可ブロック、メモリ許可スピル、または過剰なメモリ許可をキャプチャすると、突然に以前よりも多くのメモリを使用するようになったクエリについての手掛かりを得たり、既存のワークロードで発生したメモリ不足エラーの説明となり得るものを見つけたりできる可能性があります。

### <a name="in-memory-oltp-out-of-memory"></a>インメモリ OLTP のメモリ不足 

インメモリ OLTP を使用している場合に、`Error code 41805: There is insufficient memory in the resource pool '%ls' to run this operation` が発生する可能性があります。 メモリ最適化テーブルとメモリ最適化テーブル値パラメーターのデータ量を削減するか、データベースをより高いサービス目標にスケールアップしてメモリを増やします。 SQL Server インメモリ OLTP のメモリ不足の問題の詳細については、「[メモリ不足の問題の解決](/sql/relational-databases/in-memory-oltp/resolve-out-of-memory-issues)」をご覧ください。

### <a name="get-azure-sql-db-support"></a>Azure SQL DB のサポートを受ける 

Azure SQL Database でメモリ不足エラーが解消されない場合は、 [Azure サポート](https://azure.microsoft.com/support/options)サイトで **[サポートを受ける]** を選択して Azure サポート リクエストを提出します。

## <a name="next-steps"></a>次のステップ

- [SQL データベースでのインテリジェントなクエリ処理](/sql/relational-databases/performance/intelligent-query-processing)
- [クエリ処理アーキテクチャ ガイド](/sql/relational-databases/query-processing-architecture-guide)    
- [SQL Server データベース エンジンと Azure SQL Database のパフォーマンス センター](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database)  
- [Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](troubleshoot-common-errors-issues.md)
- [SQL Database と SQL Managed Instance での一時的な接続エラーのトラブルシューティング](troubleshoot-common-connectivity-issues.md)
- [インテリジェントなクエリ処理のデモ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/intelligent-query-processing)   
- [Azure SQL Database でのリソース管理](resource-limits-logical-server.md#memory)。
