---
title: Azure SQL Data Warehouse の最新世代へのアップグレード | Microsoft Docs
description: Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 673386ad236f596aa4c64fe2e8c885fb86afe170
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse をアップグレードしてパフォーマンスを最適化する
Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。

## <a name="why-upgrade"></a>アップグレードする理由
Azure Portal の [計算用に最適化] パフォーマンス レベルにシームレスにアップグレードできるようになりました。 弾力性のための最適化データ ウェアハウスをお持ちの場合は、アップグレードすることをお勧めします。 アップグレードすると、Azure ハードウェアの最新世代と拡張ストレージ アーキテクチャを使用することができます。 より高速なパフォーマンス、高いスケーラビリティ、および無制限のカラム型ストレージを利用できます。 

## <a name="applies-to"></a>適用対象
このアップグレードは、[エラスティック用に最適化] パフォーマンス レベルのデータ ウェアハウスに適用されています。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に
> [!NOTE]
> 既存の [エラスティック用に最適化] データ ウェアハウスが、[計算用に最適化] が使用可能なリージョンにない場合、サポートされるリージョンへの PowerShell を通じて [[計算用に最適化] に geo リストア](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)できます。
> 
>

1. アップグレード対象の [エラスティック用に最適化] データ ウェアハウスが一時停止されている場合は、[データ ウェアハウスを再開します](pause-and-resume-compute-portal.md)。
2. 数分間のダウンタイムに備えます。 



## <a name="start-the-upgrade"></a>アップグレードを開始する

1. Azure Portal で [エラスティック用に最適化] データ ウェアハウスに移動し、**[計算用に最適化へのアップグレード]** をクリックします。![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. 既定では、以下のマッピングを使用して、[エラスティック用に最適化] の現在のパフォーマンス レベルに基づいてデータ ウェアハウスの**推奨パフォーマンス レベルを選択**します。
    
| 弾力性のための最適化 | コンピューティング用に最適化 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. アップグレードの前に、ワークロードの実行が完了し、休止していることを確認します。 データ ウェアハウスが [計算用に最適化] データ ウェアハウスとしてオンラインに戻る前に、数分間のダウンタイムが発生します。 **[アップグレード] をクリックします**。 [計算用に最適化] パフォーマンス レベルの料金は、プレビュー期間中の現在は半額になっています。
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal で状態を確認して**アップグレードを監視**します。

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   アップグレード プロセスの最初の手順では、すべてのセッションが強制終了されるスケール操作 ("アップグレード中 - オフライン") が実行され、接続が切断されます。 
   
   アップグレード プロセスの 2 番目の手順は、データの移行 ("アップグレード中 - オンライン") です。 データの移行は、少量のオンライン バックグラウンド プロセスです。このプロセスは、列指向のデータを以前のストレージ アーキテクチャから、ローカル SSD キャッシュを活用して、新しいストレージ アーキテクチャにゆっくりと移行します。 この期間中、データ ウェアハウスはクエリと読み込みを行うためにオンラインになります。 移行されたかどうかに関係なく、すべてのデータをクエリに使用できます。 データの移行速度は、データ サイズ、パフォーマンス レベル、列ストア セグメントの数に応じて変化します。 

5. **オプションの推奨事項:** データ移行のバック グラウンド プロセスの時間を短縮するために、大規模な SLO とリソース クラスのすべての列ストア テーブルに対して [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) を実行して、データ移動を即時に強制実行することをお勧めします。 この操作は、少量のバックグラウンド プロセスとは対照的にオフラインですが、データ移行時間は大幅に短縮されるので、高品質の行グループで完了した後に新しい拡張ストレージ アーキテクチャを最大限に活用できます。 

この次のクエリでは、データの移行プロセスを短縮するために必要な Alter Index Rebuild コマンドが生成されます。

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>次の手順
アップグレードしたデータ ウェアハウスはオンラインです。 拡張アーキテクチャを利用するには、[ワークロード管理のためのリソース クラス](resource-classes-for-workload-management.md)に関する記事をご覧ください。
 
