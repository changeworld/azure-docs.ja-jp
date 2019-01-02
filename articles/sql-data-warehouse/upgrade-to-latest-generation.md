---
title: Azure SQL Data Warehouse の最新世代へのアップグレード | Microsoft Docs
description: Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1e2f1a3c46c9d343c305292a217fff5750f442fa
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682556"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse をアップグレードしてパフォーマンスを最適化する
Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。

## <a name="why-upgrade"></a>アップグレードする理由
Azure Portal で SQL Data Warehouse のコンピューティング最適化 Gen2 階層にシームレスにアップグレードできるようになりました。 コンピューティング最適化 Gen1 階層のデータ ウェアハウスをお持ちの場合は、アップグレードすることをお勧めします。 アップグレードすると、Azure ハードウェアの最新世代と拡張ストレージ アーキテクチャを使用することができます。 より高速なパフォーマンス、高いスケーラビリティ、および無制限のカラム型ストレージを利用できます。 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>適用対象
このアップグレードは、コンピューティング最適化 Gen1 階層のデータ ウェアハウスに適用されます。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に
> [!NOTE]
> コンピューティング最適化 Gen2 階層が使用可能なリージョン内に、既存のコンピューティング最適化 Gen1 階層のデータ ウェアハウスがない場合は、PowerShell を使用して、サポートされるリージョンに [geo リストア](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)します。
>
> 

1. アップグレードするコンピューティング最適化 Gen1 階層のデータ ウェアハウスが一時停止している場合は、[データ ウェアハウスを再開](pause-and-resume-compute-portal.md)します。

2. 数分間のダウンタイムに備えます。 

3. コンピューティング最適化 Gen1 パフォーマンス レベルに対するすべてのコード参照を識別し、同等のコンピューティング最適化 Gen2 パフォーマンス レベルに変更します。 アップグレードする前にコード参照を更新する必要がある 2 つの例を以下に示します。

   元の Gen1 PowerShell コマンド:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   変更後:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" が -RequestedServiceObjectiveName "DW300**c**" に変更されています
   >

   元の Gen1 T-SQL コマンド:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   変更後:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
    > [!NOTE] 
    > SERVICE_OBJECTIVE = 'DW300' が SERVICE_OBJECTIVE = 'DW300**c**' に変更されています



## <a name="start-the-upgrade"></a>アップグレードを開始する

1. Azure portal でコンピューティング最適化 Gen1 階層のデータ ウェアハウスに移動して、[タスク] タブの **[Gen2 にアップグレードします]** カードをクリックします。![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > [タスク] タブに **[Gen2 にアップグレードします]** カードが表示されない場合は、お使いのサブスクリプションの種類が現在のリージョンで制限されています。
    > [サポート チケットを提出](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)し、サブスクリプションをホワイトリストに登録してもらってください。

2. 既定では、次のマッピングを使用して、コンピューティング最適化 Gen1 階層の現在のパフォーマンス レベルに基づいて、データ ウェアハウスの**推奨パフォーマンス レベルを選択**します。

   | コンピューティング最適化 Gen1 階層 | コンピューティング最適化 Gen2 階層 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

3. アップグレードの前に、ワークロードの実行が完了し、休止していることを確認します。 データ ウェアハウスがコンピューティング最適化 Gen2 階層のデータ ウェアハウスとしてオンラインに戻る前に、数分間のダウンタイムが発生します。 **[アップグレード] をクリックします**。

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal で状態を確認して**アップグレードを監視**します。

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   アップグレード プロセスの最初の手順では、すべてのセッションが強制終了されるスケール操作 ("アップグレード中 - オフライン") が実行され、接続が切断されます。 

   アップグレード プロセスの 2 番目の手順は、データの移行 ("アップグレード中 - オンライン") です。 データの移行は、少量のオンライン バックグラウンド プロセスです。このプロセスは、列指向のデータを以前のストレージ アーキテクチャから、ローカル SSD キャッシュを活用して、新しいストレージ アーキテクチャにゆっくりと移行します。 この期間中、データ ウェアハウスはクエリと読み込みを行うためにオンラインになります。 移行されたかどうかに関係なく、すべてのデータをクエリに使用できます。 データの移行速度は、データ サイズ、パフォーマンス レベル、列ストア セグメントの数に応じて変化します。 

5. **オプションの推奨事項:** データ移行のバック グラウンド プロセスの時間を短縮するために、大規模な SLO とリソース クラスにあるクエリ対象のすべてのプライマリ列ストア テーブルに対して [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) を実行して、データ移動を即時に強制実行できます。 この操作は、少量のバックグラウンド プロセスとは対照的に**オフライン**で、テーブルの数やサイズによっては、完了までに数時間かかることがあります。しかし、データ移行時間は大幅に短縮されるので、高品質の行グループで完了した後に新しい拡張ストレージ アーキテクチャを最大限に活用できます。 

次のクエリでは、データの移行プロセスを短縮するために必要な Alter Index Rebuild コマンドが生成されます。

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
