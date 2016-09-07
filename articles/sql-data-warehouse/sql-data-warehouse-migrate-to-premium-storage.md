<properties
   pageTitle="Premium Storage への既存の Azure SQL Data Warehouse の移行 | Microsoft Azure"
   description="Premium Storage へ既存の SQL Data Warehouse を移行する手順"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/19/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Premium Storage への移行の詳細
SQL Data Warehouse には最近、[パフォーマンス予測可能性の向上を目的とした Premium Storage][] が導入されました。これにより、現在 Standard Storage 上に存在する Data Warehouse を Premium Storage に移行できるようになりました。ここでは、自動移行のしくみと移行時期のほか、ダウンタイムの発生タイミングを制御したい場合に手動で移行する方法について詳細を説明します。

Data Warehouse が複数ある場合は、以下の[自動移行スケジュール][]を参照して、それらの移行時期についても確認してください。

## ストレージの種類を確認する
次に示す日付より前に DW を作成した場合、現在は Standard Storage を使用していることになります。Standard Storage 上にある自動移行の対象となる各 Data Warehouse は、[Azure ポータル][]の Data Warehouse ブレードの上部に、次の通知が表示されます。"*予定されている Premium Storage へのアップグレードでは、サービスの停止が必ず発生します。詳細を確認してください ->*"。

| **[リージョン]** | **この日付より前に DW を作成** |
| :------------------ | :-------------------------------- |
| オーストラリア東部 | Premium Storage はまだ利用できません |
| オーストラリア南東部 | 2016 年 8 月 5 日 |
| ブラジル南部 | 2016 年 8 月 5 日 |
| カナダ中部 | 2016 年 5 月 25 日 |
| カナダ東部 | 2016 年 5 月 26 日 |
| 米国中央部 | 2016 年 5 月 26 日 |
| 中国 (東部) | Premium Storage はまだ利用できません |
| 中国 (北部) | Premium Storage はまだ利用できません |
| 東アジア | 2016 年 5 月 25 日 |
| 米国東部 | 2016 年 5 月 26 日 |
| 米国東部 2 | 2016 年 5 月 27 日 |
| インド中部 | 2016 年 5 月 27 日 |
| インド南部 | 2016 年 5 月 26 日 |
| インド西部 | Premium Storage はまだ利用できません |
| 東日本 | 2016 年 8 月 5 日 |
| 西日本 | Premium Storage はまだ利用できません |
| 米国中北部 | Premium Storage はまだ利用できません |
| 北ヨーロッパ | 2016 年 8 月 5 日 |
| 米国中南部 | 2016 年 5 月 27 日 |
| 東南アジア | 2016 年 5 月 24 日 |
| 西ヨーロッパ | 2016 年 5 月 25 日 |
| 米国中西部 | Premium Storage はまだ利用できません |
| 米国西部 | 2016 年 5 月 26 日 |
| 米国西部 2 | Premium Storage はまだ利用できません |

## 自動移行の詳細
データベースの移行は、既定では以下の[自動移行スケジュール][]に示した期間内の、ご自身のリージョンの現地時刻で午後 6 時 ～ 午前 6 時の間に行われます。移行中は、既存の Data Warehouse が使用できなくなります。移行にかかる時間は、各 Data Warehouse で 1 TB ストレージあたり約 1 時間と推定されています。また、自動移行中のどの段階においても課金は行われません。

> [AZURE.NOTE] 既存の Data Warehouse は、移行中には使用できなくなります。移行が完了すると、Data Warehouse はオンラインに戻ります。

以下に示す詳細は、移行を完了させるため、ユーザーに代わって Microsoft が実行する手順であり、ユーザー側での操作は必要ありません。この例では、現在 Standard Storage 上に "MyDW" という名前の DW が存在していると仮定します。

1.	Microsoft により、この名前は "MyDW" から "MyDW\_ DO\_NOT\_USE\_[タイムスタンプ]" に変更されます。
2.	Microsoft により、"MyDW\_DO\_NOT\_USE\_[タイムスタンプ]" が一時停止されます。 この期間中に、バックアップが実行されます。この処理の実行中に何らかの問題が発生すると、一時停止と再開が何度も繰り返される可能性があります。
3.	Microsoft により、手順 2. で作成したバックアップから "MyDW" という名前の新しい DW が Premium Storage 上に作成されます。"MyDW" は、復元が完了するまで表示されません。
4.	復元が完了すると、"MyDW" は移行前と同じ DWU に戻り、一時停止またはアクティブ状態になります。
5.	移行が完了すると、Microsoft によって "MyDW\_DO\_NOT\_USE\_[タイムスタンプ]" が削除されます。
	
> [AZURE.NOTE] 移行処理の一部として、次の各設定が引き継がれることはありません。
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

### 自動移行スケジュール
自動移行は、次の停止スケジュール期間内の午後 6 時～午前 6 時 (該当するリージョンの現地時刻) の間に行われます。

| **[リージョン]** | **開始予定日** | **終了予定日** |
| :------------------ | :--------------------------- | :--------------------------- |
| オーストラリア東部 | 未定 | 未定 |
| オーストラリア南東部 | 2016 年 8 月 10 日 | 2016 年 8 月 24 日 |
| ブラジル南部 | 2016 年 8 月 10 日 | 2016 年 8 月 24 日 |
| カナダ中部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| カナダ東部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 米国中央部 | 2016 年 6 月 23 日 | 2016 年 7 月 4 日 |
| 中国 (東部) | 未定 | 未定 |
| 中国 (北部) | 未定 | 未定 |
| 東アジア | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 米国東部 | 2016 年 6 月 23 日 | 2016 年 7 月 11 日 |
| 米国東部 2 | 2016 年 6 月 23 日 | 2016 年 7 月 8 日 |
| インド中部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| インド南部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| インド西部 | 未定 | 未定 |
| 東日本 | 2016 年 8 月 10 日 | 2016 年 8 月 24 日 |
| 西日本 | 未定 | 未定 |
| 米国中北部 | 未定 | 未定 |
| 北ヨーロッパ | 2016 年 8 月 10 日 | 2016 年 8 月 31 日 |
| 米国中南部 | 2016 年 6 月 23 日 | 2016 年 7 月 2 日 |
| 東南アジア | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 西ヨーロッパ | 2016 年 6 月 23 日 | 2016 年 7 月 8 日 |
| 米国中西部 | 2016 年 8 月 14 日 | 2016 年 8 月 31 日 |
| 米国西部 | 2016 年 6 月 23 日 | 2016 年 7 月 7 日 |
| 米国西部 2 | 2016 年 8 月 14 日 | 2016 年 8 月 31 日 |

## Premium Storage への手動移行
ダウンタイムの発生するタイミングを制御する必要がある場合は、以下の手順に従って Standard Storage 上の既存の Data Warehouse を Premium Storage に移行することができます。手動移行を選択した場合、自動移行によって競合が引き起こされる危険を回避するために、対象のリージョンで自動移行が開始する前に手動移行を完了する必要があります ([自動移行スケジュール][]を参照)。

### 手動移行の手順
ダウンタイムを制御する必要がある場合は、バックアップと復元を利用して Data Warehouse を手動で移行します。移行処理の復元部分にかかる時間は、各 DW で 1 TB ストレージあたり約 1 時間と予想されています。移行後に同じ名前を保持する必要がある場合は、[移行時の名前変更][]の手順に従ってください。

1.	自動バックアップを実行する DW を[一時停止][]します。
2.	最新のスナップショットから[復元][]します。
3.	Standard Storage 上の既存の DW を削除します。**この手順を行わないと、両方の DW に対して課金されることになります。**

> [AZURE.NOTE] 移行処理の一部として、次の各設定が引き継がれることはありません。
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

#### オプション: 移行中に名前を変更する手順 
同じ論理サーバー上の 2 つのデータベースが同じ名前を持つことはできません。SQL Data Warehouse で、DW の名前の変更機能がサポートされるようになりました。

この例では、現在 Standard Storage 上に "MyDW" という名前の DW が存在していると仮定します。

1.	ALTER DATABASE コマンドを使用して、"MyDW" を "MyDW\_BeforeMigration" のように末尾に何かを追加した名前に変更します。 このコマンドにより、すべての既存のトランザクションが終了します。この操作を成功させるには、master データベース内で行う必要があります。
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	自動バックアップを実行する "MyDW\_BeforeMigration" を[一時停止][]します。
3.	最新のスナップショットから、以前の名前 ("MyDW" など) の新しいデータベースを[復元][]します。
4.	"MyDW\_BeforeMigration" を削除します。**この手順を行わないと、両方の DW に対して課金されることになります。**

> [AZURE.NOTE] 移行処理の一部として、次の各設定が引き継がれることはありません。
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

## 次のステップ
Premium Storage の変更により、Data Warehouse の基になるアーキテクチャでデータベース BLOB ファイルの数も増えています。パフォーマンスの問題が発生した場合は、以下のスクリプトを使用して、クラスター化列ストア インデックスを再構築することをお勧めします。このスクリプトにより、既存のデータの一部が追加の BLOB に転送されます。何も行わない場合、Data Warehouse のテーブルに読み込むデータが増えるにつれて、データは自然に再配布されます。

**前提条件:**

1.	Data Warehouse を 1,000 DWU 以上で実行する必要があります ([コンピューティング能力のスケーリング][]に関する記事をご覧ください)。
2.	このスクリプトを実行するユーザーは、[mediumrc ロール][]以上である必要があります。
	1.	このロールにユーザーを追加するには、次のコマンドを実行します。
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Data Warehouse で問題が発生した場合は、[サポート チケットを作成][]し、考えられる原因を "Premium Storage への移行" としてください。

<!--Image references-->

<!--Article references-->
[自動移行スケジュール]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[サポート チケットを作成]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[一時停止]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[復元]: sql-data-warehouse-restore-database-portal.md
[移行時の名前変更]: #optional-steps-to-rename-during-migration
[コンピューティング能力のスケーリング]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[mediumrc ロール]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[パフォーマンス予測可能性の向上を目的とした Premium Storage]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->
