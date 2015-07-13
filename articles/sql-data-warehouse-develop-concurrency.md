<properties
   pageTitle="SQL Data Warehouse での同時実行とワークロード管理 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse での同時実行とワークロード管理を理解します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse での同時実行とワークロード管理
SQL Data Warehouse では、予測可能なパフォーマンスを広く提供するために、ワークロードの同時実行とコンピューティング リソースの割り当ての両方を管理するメカニズムを実装しています。

この記事では、同時実行とワークロード管理の機能がどのように実装されたか、そしてこれらの機能をデータ ウェアハウスでどのように制御するかを説明しながら、両機能の概念を紹介します。

## 同時実行
SQL Data Warehouse が、**同時実行クエリ**と**同時実行スロット**の 2 つの概念によって管理されていることを理解することは重要です。

同時実行クエリは、同時に実行されるクエリの数を指します。SQL Data Warehouse では最大 32 の**同時実行クエリ**がサポートされています。各クエリの実行は、直列クエリ (単一スレッド) または並列クエリ (マルチスレッド) かどうかに関わらず、1 つのクエリとしてカウントされます。これは固定された上限数であり、すべてのサービス レベルに適用されます。

同時実行スロットはさらに動的な概念であり、データ ウェアハウスの Data Warehouse ユニット (DWU) サービス レベル目標に対して相対的です。SQL Data Warehouse に割り当てられている DWU 数を増やすと、コンピューティング リソースが追加で割り当てられます。ただし、DWU を増やすと、使用可能な**同時実行スロット**の数も増加します。

SQL Data Warehouse は、両方のしきい値の範囲内で稼働しなければなりません。同時実行クエリの数が 32 を超えたり、同時実行スロットの数が超過した場合は、両方のしきい値が満たされるまで、クエリはキューに配置されます。

同時実行の各クエリでは、1 つまたは複数の同時実行スロットが使用されます。スロットの使用数は 2 つの要因によって変わります。

1. SQL Data Warehouse の DWU 設定
2. ユーザーが属する**リソース クラス** 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| 同時実行スロットの使用 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| 同時クエリの最大数 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| 同時実行スロットの最大数 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

リソース クラスは、クエリに割り当てられた計算リソースの管理も実行するため、SQL Data Warehouse のワークロード管理で重要な役割を果たします。これらについては、次の「ワークロード管理」セクションで説明します。

## ワークロード管理

SQL Data Warehouse では、ワークロード管理の実装の一環として、4 つの異なるリソース クラスを**データベース ロール**の形式で公開しています。

これらのロールを次に示します。

- smallrc
- mediumrc
- largerc
- xlargerc

次のクエリで、これらのロールを参照できます。

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

既定では、各ユーザーは小規模リソース クラス (smallrc) のメンバーです。ただし、いずれのユーザーも、より高いレベルの 1 つ以上のリソース クラスに追加できます。SQL Data Warehouse では、最高レベルのロールのメンバーシップをクエリ実行します。ユーザーをより高いレベルのリソース クラスに追加すると、そのユーザーのリソースが増加しますが、使用される同時実行スロット数も増えます。そのため、同時実行が制限されてしまう可能性があります。この理由は、1 つのクエリに割り当てるリソースが増えると、他のクエリに使用されるリソースをシステムが制限する必要があるからです。無限に使用することはできません。

高いレベルのリソース クラスによって管理される最も重要なリソースは、メモリです。実用的なあるあらゆる規模のデータ ウェアハウスのテーブルのほとんどでは、クラスター化列ストア インデックスが使用されます。これによって、データ ウェアハウスのワークロード処理で最高のパフォーマンスを発揮できますが、これらのインデックスを維持するにはメモリ使用量が増大します。インデックスの再構築などのデータ管理操作には、より高いリソース クラスを使用するとメリットがある場合が多いです。

メモリを増やすには、単に前述のロールのいずれかにデータベース ユーザーを追加します。

ワークロード管理のデータベース ロールに自身を追加および削除するには、`sp_addrolemember` と `sp_droprolemember` プロシージャを使用します。この操作には、`ALTER ROLE` 権限が必要です。ALTER ROLE DDL 構文は使用できません。前述のストアド プロシージャを使用する必要があります。

> [AZURE.NOTE]ワークロード管理グループにユーザーを追加したり削除したりするより、より高いリソース クラスに永続的に割り当てられた独立したログインまたはユーザーを使用して、多くのメモリを必要とする操作を開始するほうが簡単な場合があります。

次の表では、各クエリで使用可能なメモリの増加を示しています。これは、メモリ増加を実行するユーザーに適用されているリソース クラスに影響を受けます。

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->
| 使用可能なメモリ (ディスクあたり) | 優先順位 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc (既定) (s) | 中 | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 中 | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 高 | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 高 | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |


さらに、先に説明したように、ユーザーに割り当てられたリソース クラスが高いレベルであればあるほど、同時実行スロットの使用数は増加します。次の表は、指定されたリソース クラスのクエリで使用される同時実行スロットを示しています。

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| 使用される同時実行スロット数 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 同時クエリの最大数 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 同時実行スロットの最大数 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc (既定) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

処理中のクエリのワークロードは、同時実行クエリと同時実行スロットの両方のしきい値内に収まる必要があることにご注意ください。いずれかのしきい値を超えたクエリは、キューに配置されます。キューに配置されたクエリは、まず優先順位に従って、次に送信時刻に従って処理されます。

## キューに置かれたクエリの検出
同時実行キューに配置されているクエリを識別するには、常に `sys.dm_pdw_exec_requests` DMV を参照します。

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse には、同時実行を評価するための特定の待機の種類があります。

次に例を示します。
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType は、同時実行スロットのフレームワークの外に配置されたクエリを示します。DMV クエリおよび SELECT @@VERSION などのシステム関数は、localqueries の例です。

UserConcurrencyResourceType は、同時実行スロットのフレームワーク内に配置されたクエリを示します。エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。

DmsConcurrencyResourceType は、データ移動操作に起因する待機を示します。

BackupConcurrencyResourceType は、データベースのバックアップ実行中に表示されます。この種類のリソースの最大値は 1 です。同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。


現在キューに置かれたクエリを分析して、要求がどのリソースを待機しているのかを調べるには、`sys.dm_pdw_waits` DMV を参照してください。

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

特定のクエリによって使用されるリソースの待機のみを表示するには、`sys.dm_pdw_resource_waits` DMV を参照してください。リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されますが、シグナルの待機時間は、基本の SQL Server リソースに対するクエリが CPU にスケジュール設定されるまでの時間です。

```
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
WHERE	[session_id] <> SESSION_ID()
;
```

最後に、待機のこれまでの傾向を分析するために、SQL Data Warehouse では `sys.dm_pdw_wait_stats` DMV が提供されています。

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## 次のステップ
その他の開発に関するヒントについては、[development overview (開発の概要)][] を参照してください。

<!--Image references-->

<!--Article references-->
[development overview (開発の概要)]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->