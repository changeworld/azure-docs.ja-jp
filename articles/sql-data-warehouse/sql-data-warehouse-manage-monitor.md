<properties
   pageTitle="DMV を利用してワークロードを監視する | Microsoft Azure"
   description="DMV を利用してワークロードを監視するについて説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/06/2015"
   ms.author="sahajs"/>

# DMV を利用してワークロードを監視する

この記事では、動的管理ビュー (DMV) を使用し、Azure SQL データ ウェアハウスでワークロードを監視し、クエリの実行を調査する方法について説明します。




## アクセス許可

SQL Data Warehouseで、動的管理ビューに対してクエリを実行するには、**VIEW DATABASE STATE** アクセス許可が必要です。**VIEW DATABASE STATE** アクセス許可は、現在のデータベース内のすべてのオブジェクトに関する情報を返します。**VIEW DATABASE STATE** アクセス許可を特定のデータベース ユーザーに付与するには、次のクエリを実行します。

```

GRANT VIEW DATABASE STATE TO database_user;

```




## 接続を監視する

*sys.dm\_pdw\_nodes\_exec\_connections* ビューを利用し、Azure SQL Data Warehouse データベースに確立された接続に関する情報を取得できます。また、*sys.dm\_exec\_sessions* ビューは、アクティブなすべてのユーザー接続に関する情報を取得する際に役立ちます。

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


次のクエリを利用し、現在の接続に関する情報を取得できます。

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## クエリの実行を調査する
クエリが完了しなかったり、予想以上に時間がかかったりすることがあります。そのような場合、次の手順でデータを回収したり、問題を絞り込んだりできます。



### 手順 1: 調査するクエリを見つける

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

クエリの要求 ID を保存します。


  
### 手順 2: クエリがリソースを待っているのか確認する

```

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


上記のクエリの結果から要求の待機状態が表示されます。

- クエリが別のクエリからのリソースを待っている場合、状態は「**AcquireResources**」になります。
- クエリに必要なリソースがすべて揃い、待機していない場合、状態は「**Granted**」になります。その場合、クエリ手順を確認します。




### 手順 3: クエリの最長実行手順を見つける

要求 ID を利用し、分散されているすべてのクエリ手順の一覧を取得します。経過時間の合計を見て、実行時間の長い手順を見つけます。

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

実行時間の長い手順の手順インデックスを保存します。

実行時間の長いクエリ手順の *operation\_type* 列を確認します: - **SQL 操作**の手順 4a に進みます: OnOperation、RemoteOperation、ReturnOperation - **Data Movement 操作**の手順 4b に進みます: ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation



### 手順 4a: SQL 手順の実行進行状況を見つける

要求 ID と手順インデックスを利用し、クエリの SQL 手順の一環として、SQL Server クエリ分散に関する情報を取得します。ノード ID と SPID を保存します。

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


次のクエリを利用し、特定のノードの SQL 手順の SQL Server 実行計画を取得します。

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### 手順 4b: DMS 手順の実行進行状況を見つける

要求 ID と手順インデックスを利用し、各配布で実行されているデータ移動手順に関する情報を取得します。

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- *total\_elapsed\_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。 
- 実行時間の長い配布に対して、*rows\_processed* 列で、その配布から移動された行の数が他の配布の場合より大幅に大きいか確認します。これはクエリにデータ傾斜があることを表示されます。





## データ傾斜を調査する

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


このクエリの結果は、データベースの 60 の分散のそれぞれに保存されているテーブル行の数を示します。パフォーマンスを最適化するには、分散テーブルの行を配布全体で均等に広げる必要があります。詳細については、「[テーブル設計][]」を参照してください。



## 次のステップ
SQL Data Warehouse の管理に関するヒントについては、「[管理概要][]」を参照してください。

<!--Image references-->

<!--Article references-->
[管理概要]: sql-data-warehouse-overview-manage.md
[テーブル設計]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=August15_HO7-->