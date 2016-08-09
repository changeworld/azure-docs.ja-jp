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
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# DMV を利用してワークロードを監視する

この記事では、動的管理ビュー (DMV) を使用し、Azure SQL Data Warehouse でワークロードを監視し、クエリの実行を調査する方法について説明します。

## 接続を監視する

[sys.dm\_pdw\_exec\_sessions][] ビューでは、Azure SQL Data Warehouse データベースへの接続を監視できます。このビューには、アクティブなセッションのほか、最近切断されたセッションの履歴が含まれています。このビューのプライマリ キーである session\_id が、新規ログオンのたびに順次割り当てられます。

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## クエリ実行を監視する

クエリ実行を監視するには、[sys.dm\_pdw\_exec\_requests][] で始めます。このビューには進行中のクエリと、最近完了したクエリの一覧が表示されます。request\_id により各クエリが一意に識別されます。これはこのビューのプライマリ キーです。request\_id は、新しいクエリごとに順番に割り当てられます。特定の session\_id のテーブルにクエリを実行すると、そのログオンのクエリがすべて表示されます。

>[AZURE.NOTE] ストアド プロシージャでは複数の request\_id が使用されます。request\_id は順番に割り当てられます。

クエリ実行プラン、および特定のクエリの実行時間を調査するには、次の手順に従います。

### 手順 1: 調査するクエリを特定する

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;
```

上記のクエリ結果から、調査するクエリの**要求 ID を書き留めます**。

中断状態のクエリは、同時実行の制限が原因でクエリが実行されています。この詳細については、[同時実行とワークロード管理][]に関するページをご覧ください。これらのクエリは、UserConcurrencyResourceType 型の sys.dm\_pdw\_waits 待機クエリにも表示されます。クエリの待機は、ロックなど、他の理由によっても発生します。クエリがリソースを待っている場合は、[リソースを待機しているクエリの調査][]に関するトピックをご覧ください。

### 手順 2: クエリ プランの最長実行手順を見つける

要求 ID を使用して、[sys.dm\_pdw\_request\_steps][] からクエリ プランの手順の一覧を取得します。経過時間の合計を見て、実行時間の長い手順を見つけます。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

実行時間の長いクエリ手順の *operation\_type* 列を確認し、**手順インデックス**を書き留めます。

- 手順 3a の **SQL 操作** (OnOperation、RemoteOperation、ReturnOperation) に進みます。
- 手順 3b の **Data Movement 操作** (ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation) に進みます。

### 手順 3a: SQL 手順の実行進行状況を見つける

要求 ID と手順インデックスを使用して、[sys.dm\_pdw\_sql\_requests][] から詳細情報を取得します。これには、SQL Server の配布された各インスタンスに対するクエリ実行の情報が含まれます。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

クエリが実行中の場合は、[DBCC PDW\_SHOWEXECUTIONPLAN][] を使用して、特定の配布内で現在実行中の SQL 手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### 手順 3b: DMS 手順の実行進行状況を見つける

要求 ID と手順インデックスを利用し、[sys.dm\_pdw\_dms\_workers][] から各配布で実行されているデータ移動手順に関する情報を取得します。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- *total\_elapsed\_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。
- 実行時間の長い配布に対して、*rows\_processed* 列で、その配布から移動された行の数が他の配布より大幅に大きいか確認します。大きい場合は、基になるデータの傾斜を示している可能性があります。

クエリが実行中の場合は、[DBCC PDW\_SHOWEXECUTIONPLAN][] を使用して、特定の配布内で現在実行中の SQL 手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## 待機クエリを監視する

クエリがリソースを待っていることが原因で進行状況に変化がないことがわかった場合は、次のクエリを使用すると、待機対象のすべてのリソースが表示されます。Repe

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

クエリが別のクエリからのリソースを積極的に待っている場合、状態は **AcquireResources** になります。クエリに必要なリソースがすべて揃っている場合、状態は **Granted** になります。

## 次のステップ
動的管理ビュー (DMV) の詳細については、「[システム ビュー][]」をご覧ください。SQL Data Warehouse の管理に関するヒントについては、[管理の概要][]に関するページをご覧ください。ベスト プラクティスについては、[SQL Data Warehouse のベスト プラクティス][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[システム ビュー]: ./sql-data-warehouse-reference-tsql-system-views.md
[同時実行とワークロード管理]: ./sql-data-warehouse-develop-concurrency.md
[リソースを待機しているクエリの調査]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0727_2016-->