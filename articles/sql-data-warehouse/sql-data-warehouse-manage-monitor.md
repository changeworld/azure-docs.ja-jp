<properties
   pageTitle="DMV を利用してワークロードを監視する | Microsoft Azure"
   description="DMV を利用してワークロードを監視するについて説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# DMV を利用してワークロードを監視する

この記事では、動的管理ビュー (DMV) を使用し、Azure SQL Data Warehouse でワークロードを監視し、クエリの実行を調査する方法について説明します。

## 接続を監視する

[sys.dm\_pdw\_exec\_sessions][] ビューでは、Azure SQL Data Warehouse データベースへの接続を監視できます。このビューには、アクティブなセッションのほか、最近切断されたセッションの履歴が含まれています。このビューのプライマリ キーである session\_id が、新規ログオンのたびに順次割り当てられます。

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## クエリの実行を調査する
クエリ実行を監視するには、[sys.dm\_pdw\_exec\_requests][] で始めます。このビューには進行中のクエリと、最近完了したクエリの一覧が表示されます。request\_id により各クエリが一意に識別されます。これはこのビューのプライマリ キーです。request\_id は、新しいクエリごとに順番に割り当てられます。特定の session\_id のテーブルにクエリを実行すると、そのログオンのクエリがすべて表示されます。

特定のクエリのクエリ実行を調査する必要がある場合に一般的に使用する手順を次に示します。

### 手順 1: 調査するクエリを見つける

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find 10 queries which ran the longest
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

調査するクエリの要求 ID を書き留めます。

### 手順 2: クエリがリソースを待っているのか確認する

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

上記のクエリの結果からクエリの待機状態が表示されます。

- クエリが別のクエリからのリソースを待っている場合、状態は「**AcquireResources**」になります。
- クエリに必要なリソースがすべて揃い、待機していない場合、状態は「**Granted**」になります。その場合、クエリ手順を確認します。

### 手順 3: クエリ プランの最長実行手順を見つける

要求 ID を使用して、[sys.dm\_pdw\_request\_steps][] からクエリ プランの手順の一覧を取得します。経過時間の合計を見て、実行時間の長い手順を見つけます。

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

実行時間の長い手順の手順インデックスを保存します。

実行時間の長いクエリ手順の *operation\_type* 列を確認します。

- 手順 4a の **SQL 操作** (OnOperation、RemoteOperation、ReturnOperation) に進みます。
- 手順 4b の **Data Movement 操作** (ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation) に進みます。

### 手順 4a: SQL 手順の実行進行状況を見つける

要求 ID と手順インデックスを使用して、[sys.dm\_pdw\_sql\_requests][] から情報を取得します。これには、SQL Server の配布されたインスタンスに対するクエリ実行の詳細が含まれます。クエリが実行中で、SQL Server の配布からプランを取得する必要がある場合は、配布 ID および SPID を書き留めます。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


クエリが現在実行中の場合、[DBCC PDW\_SHOWEXECUTIONPLAN][] を使用して、特定の配布について現在実行中の SQL 手順の SQL Server 実行プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### 手順 4b: DMS 手順の実行進行状況を見つける

要求 ID と手順インデックスを利用し、[sys.dm\_pdw\_dms\_workers][] から各配布で実行されているデータ移動手順に関する情報を取得します。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- *total\_elapsed\_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。
- 実行時間の長い配布に対して、*rows\_processed* 列で、その配布から移動された行の数が他の配布より大幅に大きいか確認します。大きい場合は、基になるデータの傾斜を示している可能性があります。

クエリが現在実行中の場合、[DBCC PDW\_SHOWEXECUTIONPLAN][] を使用して、特定の配布について現在実行中の DMS 手順の SQL Server 実行プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);

```

## 次のステップ
動的管理ビュー (DMV) の詳細については、「[システム ビュー][]」を参照してください。SQL Data Warehouse の管理に関するヒントについては、「[SQL Data Warehouse の管理ツール][]」を参照してください。ベスト プラクティスについては、「[SQL Data Warehouse Best Practices (SQL Data Warehouse ベスト プラクティス)][]」を参照してください。

<!--Image references-->

<!--Article references-->
[manage data skew for distributed tables]: sql-data-warehouse-manage-distributed-data-skew.md
[SQL Data Warehouse の管理ツール]: sql-data-warehouse-overview-manage.md
[SQL Data Warehouse Best Practices (SQL Data Warehouse ベスト プラクティス)]: sql-data-warehouse-best-practices.md
[システム ビュー]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0504_2016-->