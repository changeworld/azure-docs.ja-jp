---
title: "ワークロード管理用のリソース クラス - Azure SQL Data Warehouse | Microsoft Docs"
description: "Azure SQL Data Warehouse のクエリで、リソース クラスを使用して、同時実行とコンピューティング リソース を管理するためのガイダンスです。"
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
ms.openlocfilehash: c76fb73c9beda93c407d1af29e157682c7fe58c0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="resource-classes-for-workload-management"></a>ワークロード管理用のリソース クラス
Azure SQL Data Warehouse のクエリで、リソース クラスを使用して、同時に実行される同時クエリの数とコンピューティング リソース を管理するためのガイダンスです。
 
## <a name="what-is-workload-management"></a>ワークロード管理とは
ワークロード管理は、すべてのクエリの全体的なパフォーマンスを最適化する機能です。 適切に調整されたワークロードは、クエリと読み込み操作を、コンピューティング集中型であるか IO 集約型であるかに関係なく、効率的に実行します。 

SQL Data Warehouse は、マルチ ユーザー環境向けのワークロード管理機能を備えています。 データ ウェアハウスは、マルチテナント ワークロードを想定していません。

## <a name="what-are-resource-classes"></a>リソース クラスとは
リソース クラスは、クエリの実行を制御するあらかじめ決められたリソースの制限です。 SQL Data Warehouse は、リソース クラスに従って、各クエリのコンピューティング リソースを制限します。 

リソース クラスを使用して、データ ウェアハウスのワークロードの全体的なパフォーマンスを管理できます。リソース クラスを効果的に使用して、同時に実行されるクエリの数と各クエリに割り当てられるコンピューティング リソースに制限を設定することで、ワークロードを管理できます。 

- 小規模リソース クラスは、コンピューティング リソースを少し使用しますが、クエリの全体的な同時実行性を大きくすることができます。
- 大規模リソース クラスは、コンピューティング リソースをたくさん使用しますが、クエリの同時実行性は制限されます。

リソース クラスは、データ管理と操作アクティビティのために設計されています。 大規模な結合と並べ替えがある場合に、システムがディスクを使用せずにメモリ内でのみクエリを実行できるため、非常に複雑なクエリにとってもメリットがあります。

リソース クラスによって、次の操作が制御されます。

* INSERT-SELECT、UPDATE、DELETE
* SELECT (ユーザー テーブルのクエリを実行する場合)
* ALTER INDEX - REBUILD または REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* データの読み込み
* Data Movement Service (DMS) によって実行されるデータ移動操作

> [!NOTE]  
> 動的管理ビュー (DMV) やその他のシステム ビューの SELECT ステートメントは、同時実行の制限の対象になりません。 ユーザーは、システムで実行されるクエリの数にとらわれずにシステムを監視できます。
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>静的リソース クラスと動的リソース クラス

リソース クラスには、動的と静的の 2 種類があります。

- **静的リソース クラス**は、現在のサービス レベルに関係なく、同じメモリ量を割り当てます。これは[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)で測定されます。 この静的割り当ては、大きなサービス レベルでは、各リソース クラスでより多くのクエリを実行できることを意味します。  静的リソース クラスの名前は、staticrc10、staticrc20、staticrc30、staticrc40、staticrc50、staticrc60、staticrc70、および staticrc80 です。 これらのリソース クラスは、リソース クラスを追加することでコンピューティング リソースを追加取得するソリューションに最適です。

- **動的リソース クラス**は、現在のサービス レベルに応じたメモリ量を割り当てます。 より大きなサービス レベルにスケールアップすると、クエリは自動的により多くのメモリを取得します。 動的リソース クラスの名前は、smallrc、mediumrc、largerc、および xlargerc です。 これらのリソース クラスは、コンピューティング スケールを増やすことでリソースを追加取得するソリューションに最適です。 

[パフォーマンス レベル](performance-tiers.md)も同じリソース クラス名を使用しますが、[メモリと同時実行の仕様](performance-tiers.md)が異なります。 


## <a name="assigning-resource-classes"></a>リソース クラスの割り当て

リソース クラスは、ユーザーにデータベース ロールを割り当てることによって実装されます。 ユーザーがクエリを実行すると、クエリはユーザーのリソース クラスで実行されます。 たとえば、ユーザーが smallrc または staticrc10 データベース ロールのメンバーの場合、クエリは少量のメモリを使用して実行されます。 データベース ユーザーが xlargerc または staticrc80 データベース ロールのメンバーの場合、クエリは大量のメモリを使用して実行されます。 

ユーザーのリソース クラスを大きくするには、[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ストアド プロシージャを使用します。 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

リソース クラスを小さくするには、[sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) を使用します。  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

サービス管理者のリソース クラスは固定され、変更できません。  サービス管理者はプロビジョニング プロセス中に作成されるユーザーです。

> [!NOTE]
> Active Directory 管理者として定義されたユーザーまたはグループは、サービス管理者でもあります。
>
>

### <a name="default-resource-class"></a>既定のリソース クラス
既定では、各ユーザーは小規模リソース クラス **smallrc** のメンバーです。 

### <a name="resource-class-precedence"></a>リソース クラスの優先順位
ユーザーは、複数のリソース クラスのメンバーになることができます。 ユーザーが 1 つ以上のリソース クラスに属す場合:

- 動的リソース クラスが静的リソースクラスに優先します。 たとえば、ユーザーが mediumrc (動的) と staticrc80 (静的) のメンバーの場合、クエリは mediumrc で実行されます。
- 大規模リソース クラスが小規模リソースクラスに優先します。 たとえば、ユーザーが mediumrc と largerc のメンバーの場合、クエリは largerc で実行されます。 同様に、ユーザーが staticrc20 と statirc80 の両方のメンバーの場合、クエリは staticrc80 に対するリソースの割り当てを使用して実行されます。

### <a name="queries-exempt-from-resource-classes"></a>リソース クラスの対象外となるクエリ
一部のクエリは、ユーザーが大規模リソース クラスのメンバーであっても、常に smallrc リソース クラスで実行されます。 これらの対象外のクエリは、同時実行の制限にはカウントされません。 たとえば、同時実行の制限が 16 の場合、利用可能な同時実行スロットに影響を与えることなく、システム ビューから多数のユーザーを選択できます。

次のステートメントはリソース クラスの対象外であり、常に smallrc で実行されます。

* CREATE または DROP TABLE
* ALTER TABLE ...SWITCH、SPLIT、または MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE、UPDATE、または DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE、ALTER、または DROP USER
* CREATE、ALTER、または DROP PROCEDURE
* CREATE または DROP VIEW
* INSERT VALUES
* システム ビューおよび DMV からの SELECT
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>Recommendations
お勧めするのは、特定の種類のクエリまたは読み込み操作を実行する専用のユーザーを作成することです。 頻繁にリソース クラスを変更する代わりに、そのユーザーに永続的なリソース クラスを指定します。 静的リソース クラスを指定すると、ワークロード全体が制御しやすくなります。さらに、動的リソース クラスを検討する前に、まず静的リソース クラスを使用することをお勧めします。

### <a name="resource-classes-for-load-users"></a>読み込みユーザー用のリソース クラス
`CREATE TABLE` は、既定により、クラスター化列ストア インデックスを使用します。 列ストア インデックスへのデータの圧縮はメモリを消費する操作であり、メモリ不足によってインデックスの品質が低下する可能性があります。 そのため、データの読み込み時より高いリソース クラスが必要になる可能性があります。 読み込み時に十分なメモリがあることを保証するために、読み込みを実行するように指定されたユーザーを作成し、そのユーザーに高いリソース クラスを割り当てることができます。

読み込みを効率的に処理するために必要なメモリは、読み込まれるテーブルの性質とデータ量によって異なります。 メモリ要件の詳細については、「[列ストアの行グループの品質を最大限にする](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)」を参照してください。

メモリ要件を決定したら、静的または動的なリソース クラスのどちらを読み込みユーザーに割り当てるかを選択します。

- テーブルのメモリ要件が特定の範囲に収まる場合は、静的リソース クラスを使用します。 読み込みは適切なメモリで実行されます。 データ ウェアハウスをスケーリングしても、読み込みにさらに多くのメモリが必要になることはありません。 静的リソース クラスを使用しているため、メモリの割り当てが変化することはありません。 この一貫性によってメモリは節約され、より多くのクエリを同時に実行できます。 新しいソリューションでは、制御しやすいため、まず静的リソース クラスを使用することをお勧めします。
- テーブルのメモリ要件が大きく異なる場合は、動的なリソース クラスを使用します。 読み込みで、現在の DWU または cDWU レベルによって提供されるメモリを上回るメモリが必要になることがあります。 そのため、データ ウェアハウスをスケーリングすると、読み込み操作に多くのメモリが追加され、読み込みを高速で実行できます。

### <a name="resource-classes-for-queries"></a>クエリ ユーザー用のリソース クラス

クエリには、コンピューティング集中型とそうでないものがあります。  

- クエリは複雑だが、高い同時実行性を必要としない場合は、動的リソース クラスを選択します。  たとえば、日次または週次レポートの生成は、リソースに対する常時発生するものではないニーズです。 レポートが大量のデータを処理する場合は、データ ウェアハウスをスケーリングすることで、、ユーザーの既存のリソース クラスにより多くのメモリが提供されます。
- 要求されるリソースが日中変化する場合は、静的リソース クラスを選択します。 たとえば、データ ウェアハウスに対して多数のユーザーがクエリを実行する場合、静的リソース クラスは功を奏します。 データ ウェアハウスをスケーリングしても、ユーザーに割り当てられるメモリの量は変化しません。 その結果、より多くのクエリをシステムで並列に実行できます。

適切なメモリ許可の選択は、クエリされるデータの量、テーブル スキーマの性質、さまざまな結合、選択、およびグループ述語などの多くの要因に依存します。 通常は、メモリ割り当て量を増やすと、クエリの実行時間が短縮されますが、全体的な同時実行性は減少します。 同時実行性が問題でない場合は、メモリを過剰に割り当てても、スループットに影響が出ることはありません。 

パフォーマンスを調整するには、複数のリソース クラスを使用します。 次のセクションで、最適なリソース クラスを見つけるために役立つストアド プロシージャについて説明します。

## <a name="example-code-for-finding-the-best-resource-class"></a>最適なリソース クラスを見つけるためのコードの例
 
次のストアド プロシージャを使用して、特定の SLO の同時実行性とリソース クラスあたりのメモリ割り当てを割り出せば、特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作に最適なリソース クラスを特定することができます。

このストアド プロシージャの目的を次に示します。  
1. 特定の SLO のリソース クラスあたりの同時実行性とメモリ許可を特定すること。 次の例に示すように、スキーマとテーブル名の両方に NULL を指定する必要があります。  
2. 特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作 (読み込み、テーブルのコピー、インデックスの再構築など) にとって、最適に近いリソース クラスを特定すること。 このストアド プロシージャでは、必要なメモリ許可を割り出すために、テーブル スキーマを使用します。

### <a name="dependencies--restrictions"></a>依存関係と制限事項:
- このストアド プロシージャは、分割型 CCI テーブルのメモリ要件を計算するものではありません。    
- このストアド プロシージャでは、CTAS/INSERT-SELECT の SELECT 部分のメモリ要件は計算に入れられておらず、単純な SELECT であると想定しています。
- このストアド プロシージャでは、このストアド プロシージャが作成されたセッションで使用できる一時テーブルを使用します。    
- このストアド プロシージャは、現在の環境 (ハードウェア構成、DMS 構成など) に依存しており、そのいずれかが変更された場合は正しく動作しません。  
- このストアド プロシージャは、既存の同時実行の制限に依存しており、それが変更された場合は正しく動作しません。  
- このストアド プロシージャは、既存のリソース クラスに依存しており、それが変更された場合は正しく動作しません。  

>  [!NOTE]  
>  指定されたパラメーターを使用してストアド プロシージャを実行しても出力が得られない場合は、次の 2 つの理由が考えられます。 <br />1.DW パラメーターに無効な SLO 値が含まれている <br />2.テーブルに対する CCI 操作にマッチするリソース クラスがありません。 <br />たとえば、DW100 で利用可能な最大のメモリ許可が 400 MB であり、テーブル スキーマのサイズが 400 MB の要件を超える場合です。
      
### <a name="usage-example"></a>使用例:
構文:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:DW DB から現在の DWU を抽出するため NULL パラメーターを指定するか、'DW100' の形式でサポートされている DWU を指定します。
2. @SCHEMA_NAME:テーブルのスキーマ名を指定します。
3. @TABLE_NAME:必要なテーブル名を指定します。

このストアド プロシージャの実行例:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

次のステートメントは、前の例で使用される Table1 を作成します。
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>ストアド プロシージャの定義

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>次の手順
データベース ユーザーの管理とセキュリティの詳細については、「[SQL Data Warehouse でのデータベース保護][Secure a database in SQL Data Warehouse]」を参照してください。 大規模なリソース クラスを使用してクラスター化列ストア インデックスの品質を向上させる方法については、[列ストアを圧縮するためのメモリの最適化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
