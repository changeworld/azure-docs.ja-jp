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
   ms.date="07/15/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL Data Warehouse での同時実行とワークロード管理

SQL Data Warehouse では、予測可能なパフォーマンスを大規模に実現するために、ユーザーが、メモリと CPU の優先順位付けのようなリソース割り当てに加えて、同時実行レベルを制御できるようになっています。この記事では、同時実行とワークロード管理の機能がどのように実装されたか、そしてこれらの機能をデータ ウェアハウスでどのように制御できるかについて説明しながら、両機能の概念を紹介します。SQL Data Warehouse のワークロード管理は、マルチユーザー環境をサポートすることを目的としています。マルチテナント ワークロードは想定されていません。

## 同時実行の制限

SQL Data Warehouse では、最大 1,024 個の同時接続が可能です。1,024 のすべての接続でクエリを同時に送信することができます。ただし、スループットを最適化する目的で、各クエリに与えられるメモリ許可が最小限になるようにするために、一部のクエリがキューに配置される場合があります。クエリの実行時にキューに配置されます。同時実行の制限に達したときにクエリをキューに配置することによって、アクティブなクエリが絶対に必要なメモリ リソースに必ずアクセスできるようになるため、全体のスループットが向上します。

同時実行の制限は、**同時実行クエリ**と**同時実行スロット**の 2 つの概念によって管理されます。クエリは、クエリの同時実行の制限下および同時実行スロット割り当て内で実行する必要があります。

- **同時実行クエリ**は、単に同時に実行されるクエリの数を指します。SQL Data Warehouse では、DW1000 を超える大きな DW サイズ上で最大 32 個の**同時実行クエリ**をサポートしています。ただし、同時実行クエリの数は DWU の数によって異なるため、DWU ごとの制限を次の表に示します。
- **同時実行スロット**は、より動的な概念です。クエリごとに、1 つまたは複数の同時実行スロットを使用できます。クエリによって使用されるスロットの正確な数は、SQL Data Warehouse のサイズとクエリの[リソース クラス](#resource-classes)に依存します。

次の表に、同時実行クエリと同時実行スロットの両方の制限を示します。

### 同時実行の制限

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| 同時クエリの最大数 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 同時実行スロットの最大数 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

これらのいずれかのしきい値に達すると、新しいクエリはキューに配置されます。他のクエリが完了してクエリとスロットの数が制限値を下回ると、キューに配置されたクエリが先入先出方式で実行されます。

> [AZURE.NOTE]  動的管理ビュー (DMV) とカタログ ビューでのみ実行される SELECT クエリは、どの同時実行の制限によっても管理**されません**。そのため、ユーザーは、システムで実行されるクエリの数にとらわれずにシステムを監視できます。

## リソース クラス

リソース クラスは SQL Data Warehouse のワークロード管理の核となる要素です。これらを使用することで、特定のユーザーによって実行されるクエリにより多くのメモリまたは CPU サイクルを割り当てることができます。ユーザーに割り当てることができるリソース クラスは 4 つあり、それぞれが**データベース ロール**に対応しています。4 つのリソース クラスとは、**smallrc、mediumrc、largerc、xlargerc** です。smallrc のユーザーにはより少ないメモリが割り当てられ、その結果、より多くの同時実行が可能になります。これに対し、xlargerc に割り当てられたユーザーには多くのメモリが与えられるため、同時実行が許可されるクエリの数は少なくなります。

既定では、各ユーザーは小規模リソース クラス (smallrc) のメンバーです。プロシージャ `sp_addrolemember` は、リソース クラスのサイズを大きくするために使用します。`sp_droprolemember` は、リソース クラスのサイズを小さくするために使用します。たとえば、次のコマンドは、loaduser のリソース クラスをサイズの大きな largerc に割り当てます。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

お勧めの方法は、ユーザーのリソース クラスを変更するのではなく、特定のリソース クラスに永続的に割り当てられるユーザーを作成することです。たとえば、クラスター化列ストア テーブルへの読み込みでは、より多くのメモリを割り当てることでより高品質のインデックスを作成できます。読み込み時により多くのメモリにアクセスできるようにするには、データの読み込み専用のユーザーを作成し、このユーザーを永続的に上位のリソース クラスに割り当てます。

クエリの中には、多くのメモリを割り当ててもメリットがないために、リソース クラスの割り当てがシステムで無視されて、常に小規模リソース クラスで実行されるものも少数あります。これらのクエリが常に小規模リソース クラスで実行されるように強制することで、同時実行スロットの空きが少なくなっているときにこれらのクエリを実行でき、これらのクエリによってスロットが必要以上に使用されるのを防ぐことができます。これらの[リソース クラスの例外](#resource-class-exceptions)については、この記事の後の方で説明します。

リソース クラスに関する詳細をもう少し以下に示します。

- ユーザーのリソース クラスを変更するには、`ALTER ROLE` アクセス許可が必要です。
- ユーザーは 1 つまたは複数の上位のリソース クラスに割り当てることができますが、割り当てられた最上位のリソース クラスの属性がユーザーに設定されます。つまり、ユーザーが mediumrc と largerc の両方に割り当てられている場合、上位のリソース クラスである largerc が優先されるリソース クラスとなります。
- システム管理ユーザーのリソース クラスは変更できません。
 
ユーザーの作成とリソース クラスへの割り当てに関する詳細および例については、この記事の「[ユーザーの管理](#Managing-users)」セクションを参照してください。

## メモリの割り当て

ユーザーのリソース クラスを引き上げることには長所と短所があります。ユーザーのリソース クラスを引き上げると、クエリで利用可能なメモリが増え、クエリの実行速度が上がりますが、リソース クラスの引き上げは、同時実行可能なクエリの数を減らしもします。これは、大量のメモリを 1 つのクエリに割り当てるか、同様にメモリの割り当てが必要な他のクエリの同時実行を許可するかのトレードオフです。あるユーザーにクエリのメモリが大量に割り当てられている場合、他のユーザーがクエリを実行するために同じメモリにアクセスすることはできなくなります。

次の表に、DWU とリソース クラスごとに各ディストリビューションに割り当てられるメモリを示します。SQL Data Warehouse では、データベースあたり 60 個のディストリビューションがあります。たとえば、xlarge リソース クラスの DW2000 で実行されているクエリは、60 個の分散データベースそれぞれの 6,400 MB にアクセスします。

### ディストリビューションあたりのメモリ割り当て (MB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 3,200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 6,400 |
| xlargerc | 400 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 3,200 | 6,400 | 6,400 | 12,800 |


上記と同じ例を使用した場合、xlarge リソース クラスの DW2000 で実行されているクエリには、システム全体で合計 375 GB のメモリが割り当てられます (6,400 MB * 60 ディストリビューション / 1,024 (GB 単位))。

### システム全体のメモリ割り当て (GB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## 使用される同時実行スロット数

前述のように、上位のリソース クラスほど多くのメモリが割り当てられます。メモリは固定のリソースであるため、1 つのクエリに割り当てられるメモリが多くなるほど、サポートされる同時実行性が低くなります。次の表で、これまでに説明したすべての概念を 1 つにまとめ、各 DWU で利用可能な同時実行スロットの数と各リソース クラスで使用されるスロットの数を示します。

### 同時実行スロットの割り当てと使用数

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **割り当て** | | | | | | | | | | | | |
| 同時クエリの最大数 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 同時実行スロットの最大数 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |
| **スロットの使用数** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

この表から、DW1000 として実行されている SQL Data Warehouse では、全部で 40 個の同時実行スロットに最大 32 個の同時実行クエリが提供されます。すべてのユーザーが小規模リソース クラスで実行している場合、クエリごとに 1 つの同時実行スロットを利用できるため、32 個のクエリを同時実行できます。すべてのユーザーが中規模リソース クラスで実行している場合、各ユーザーに総メモリの 47 GB の割り当てについてディストリビューションあたり 800 MB の領域が割り当てられ、中規模リソース クラスのユーザー全体での同時実行は 8 ユーザーに制限されます。

## クエリの重要度

内部的に、リソース クラスの動作を制御するワークロード グループは合計で 8 つあります。ただし、8 つのワークロード グループのうちの 4 つだけが特定の DWU で利用されます。各ワークロード グループは smallrc、mediumrc、largerc、または xlargerc に割り当てられるため、これは当然のことです。これらの背後にあるワークロード グループを理解するうえで、これらのワークロード グループの一部が上位の**重要度**に設定されることに注意する必要があります。重要度は、CPU のスケジュール設定に使用されます。重要度が "高" のクエリは、重要度が "中" のクエリと比べて 3 倍の CPU サイクルを獲得します。そのため、同時実行スロットのマッピングにより、CPU の重要度も決まります。16 個以上のスロットを使用するクエリは、"高" 重要度として実行されます。

各ワークロード グループの重要度のマッピングを次に示します。

| ワークロード グループ | 同時実行スロットのマッピング | 重要度のマッピング |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | 中 |
| SloDWGroupC01 | 2 | 中 |
| SloDWGroupC02 | 4 | 中 |
| SloDWGroupC03 | 8 | 中 |
| SloDWGroupC04 | 16 | 高 |
| SloDWGroupC05 | 32 | 高 |
| SloDWGroupC06 | 64 | 高 |
| SloDWGroupC07 | 128 | 高 |

DW500 の SQL Data Warehouse の場合、アクティブなワークロード グループは次のようにリソース クラスにマッピングされます。

| リソース クラス | ワークロード グループ | 使用される同時実行スロット数 | [重要度] |
| :--------------- | :------------- | :--------------------:   | :--------- |
| smallrc | SloDWGroupC00 | 1 | 中 |
| mediumrc | SloDWGroupC02 | 4 | 中 |
| largerc | SloDWGroupC03 | 8 | 中 |
| xlargerc | SloDWGroupC04 | 16 | 高 |


次の DMV クエリを使用すると、リソース ガバナーの観点からメモリ リソースの割り当ての違いを詳細に確認できます。また、トラブルシューティングを行うときにワークロード グループのアクティブな使用状況と過去の使用状況を分析することもできます。

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

## リソース クラスの例外

ほとんどのクエリではリソース クラスが考慮されますが、いくつかの例外があります。これは通常、アクションの実行に必要なリソースが不足している場合に発生します。つまり、一般的に、上位のリソース クラスによって割り当てられるより多くのメモリがクエリで利用されない場合が例外になります。このような場合、ユーザーに割り当てられたリソース クラスに関係なく、既定値または小規模リソース クラス (smallrc) が常に使用されます。たとえば、`CREATE LOGIN` は常に smallrc で実行されます。この操作の実行に必要なリソースが非常に低い場合、同時実行スロット モデルにクエリを含めても効果はありません。この処理に大量のメモリを事前に割り当てるのは無駄になります。`CREATE LOGIN` を同時実行スロット モデルから除外すると、SQL Data Warehouse をはるかに効率的に活用できます。

次のステートメントでは、リソース クラスは**考慮されません。**

- CREATE または DROP TABLE
- ALTER TABLE ...SWITCH、SPLIT、または MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE、UPDATE、または DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE、ALTER、または DROP USER
- CREATE、ALTER、または DROP PROCEDURE
- CREATE または DROP VIEW
- INSERT VALUES
- システム ビューおよび DMV からの SELECT
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

### 同時実行の制限が考慮されるクエリ

エンド ユーザーのクエリの大半がリソース クラスで管理される可能性が高いことにご注意ください。一般的に、処理中のクエリのワークロードは、プラットフォームで特別に除外されている場合を除き、同時実行クエリと同時実行スロットの両方のしきい値内に収まる必要があります。エンド ユーザーは、同時実行スロット モデルからクエリ除外を選択することはできません。いずれかのしきい値を超えたクエリは、キューに配置されます。キューに配置されたクエリは、まず優先順位に従って、次に送信時刻に従って処理されます。

繰り返しますが、次のステートメントではリソース クラスが**考慮**されます。

- INSERT-SELECT
- UPDATE
- 削除
- SELECT (ユーザー テーブルのクエリを実行する場合)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- データの読み込み
- Data Movement Service (DMS) によって実行されるデータ移動操作


## ユーザーの管理

1. **ログインを作成する:** SQL Data Warehouse の**マスター** データベースへの接続を開き、次のコマンドを実行します。
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Azure SQL Database と Azure SQL Data Warehouse の両方のマスター データベースに、ログイン用のユーザーを作成することをお勧めします。このレベルでは 2 つのサーバー ロールを利用できます。これらのロールは、メンバーシップを許可するためには、ログインがマスターにユーザーを持つ必要があります。これらのロールは、`Loginmanager` と `dbmanager` です。これらのロールは、Azure SQL Database と SQL Data Warehouse の両方で、ログインを管理し、データベースを作成する権限を付与します。この点が SQL Server とは異なります。詳細については、[Azure SQL Database におけるデータベースとログインの管理][]に関する記事を参照してください。

2. **ユーザー アカウントを作成する:** **SQL Data Warehouse** データベースへの接続を開き、次のコマンドを実行します。

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **アクセス許可を付与する:** 次の例は、SQL Data Warehouse データベースで `CONTROL` を付与します。データベース レベルの `CONTROL` は、SQL Server の db\_owner に相当します。

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **リソース クラスを増やす:** 増加ワークロード管理ロールにユーザーを追加するには、次のクエリを使用します。

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **リソース クラスを減らす:** ワークロード管理ロールからユーザーを削除するには、次のクエリを使用します。

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] smallrc からユーザーを削除することはできません。

## キューに配置されたクエリの検出とその他の DMV

`sys.dm_pdw_exec_requests` DMV を使用すると、同時実行キューで待機しているクエリを識別できます。

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

ワークロード管理ロールを確認するには、`sys.database_principals` を使用します。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

次のクエリは、各ユーザーが割り当てられているロールを示します。

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse には、次の待機の種類があります。

- LocalQueriesConcurrencyResourceType: 同時実行スロットのフレームワークの外に配置されたクエリ。DMV クエリと、`SELECT @@VERSION` のようなシステム関数は、ローカル クエリの例です。
- UserConcurrencyResourceType: 同時実行スロットのフレームワーク内に配置されたクエリ。エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。
- DmsConcurrencyResourceType: データ移動操作に起因する待機。
- BackupConcurrencyResourceType: この待機は、データベースがバックアップ中であることを示します。この種類のリソースの最大値は 1 です。同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。

`sys.dm_pdw_waits` DMV を使用すると、要求がどのリソースを待っているのかを調べることができます。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV は、特定のクエリによって使用されるリソースの待機のみを表示します。リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されます。これに対し、シグナルの待機時間は、基本の SQL Server リソースに対するクエリが CPU にスケジュール設定されるまでの時間です。

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
WHERE	[session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV を使用すると、待機のこれまでの傾向を分析できます。

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## 次のステップ

データベース ユーザーの管理とセキュリティの詳細については、「[SQL Data Warehouse でのデータベース保護][]」を参照してください。大規模なリソース クラスを使用して、クラスター化された列ストア インデックスの品質を向上させる方法については、「[セグメントの品質を向上させるためのインデックスの再構築]」を参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse でのデータベース保護]: ./sql-data-warehouse-overview-manage-security.md
[セグメントの品質を向上させるためのインデックスの再構築]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Azure SQL Database におけるデータベースとログインの管理]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0720_2016-->