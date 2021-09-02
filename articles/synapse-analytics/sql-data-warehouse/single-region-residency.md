---
title: 単一リージョン所在地
description: Azure Synapse Analytics 内の専用 SQL プール (以前の SQL DW) の単一リージョン所在地を構成するための攻略ガイド
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9fd0860fba93f2aa45616707c791aef498fb06e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580724"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics 内の専用 SQL プール (以前の SQL DW) の単一リージョン所在地を構成する

この記事では、ローカル冗長ストレージにデータベースをプロビジョニングすることで、地理的な境界内にデータが確実に留まるようにします。 この記事は、Azure リージョン ペアの配置先が国外であるリージョン内の専用 SQL プールのみを対象としています。 この記事の手順を実施すると、ほとんどのデータおよびすべてのバックアップが[ペア リージョン](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)にレプリケートされなくなります。 それでも論理サーバー名や論理データベース名、リソース グループ名など、一部のメタデータは依然としてペア リージョンにレプリケートされます。これは既定の動作であり、変更することはできません。 

単一リージョン所在地を実現するには、SQL プールのプロビジョニング時の geo 冗長オプションに [いいえ] を選択して、専用 SQL プール (以前の SQL DW) をローカル冗長ストレージにプロビジョニングします。 SQL プールが既にプロビジョニングされて geo 冗長ストレージに存在する場合は、SQL プールを復元してください。復元プロセス中、geo 冗長ストレージに関して [いいえ] を選択できます。 以降、両方のシナリオの手順について詳しく説明します。 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Azure portal を使用してローカル冗長ストレージに専用 SQL プール (以前の SQL DW) をプロビジョニングする

専用 SQL プール (以前の SQL DW) をローカル冗長ストレージにプロビジョニングするには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。
1. **専用 SQL プール (以前の SQL DW)** を検索します。

   ![新しい DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. [追加] をクリックし、 **[基本]** タブ*で要求された情報を入力します。

1. リージョン ペアが別の地域にあるリージョン (東南アジア) にサーバーが配置されている場合、必要に応じて [geo 冗長] をオプトアウトすることができます。 

   ![データの保存場所](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. **[次へ: ネットワーク]** を選択してパブリックまたはプライベート エンドポイントを構成します。 

1. **[次へ: 追加設定]** を選択して、バックアップやサンプルから復元するか、空のデータベースを作成します。  

1. **[次へ: タグ]** を選択して、SQL プールを分類するためのタグを構成します。 

1. **[次へ: 確認および作成]** を選択します。 データ所在地を確保するために、ストレージ冗長が正しく選択されていることを確認してください。 

    ![単一リージョンプールを作成する](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. **［作成］** を選択します  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>PowerShell を使用してローカル冗長ストレージに専用 SQL プール (以前の SQL DW) をプロビジョニングする
新しい専用 SQL プールを PowerShell で作成するには、[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。

1. 開始する前に、必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元するデータベースを含むサブスクリプションを選択します。
5. ローカル冗長ストレージに新しいデータベースを作成します。
1. ローカル冗長ストレージにデータベースが正常に作成されたことを確認します。  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Azure portal を使用してローカル冗長ストレージに専用 SQL プール (以前の SQL DW) を復元する
1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 復元元の専用 SQL プールに移動します。

1. [概要] ブレードの上部にある **[復元]** を選択します。

1. 復元ポイントを選択して、どの時点から復元するかを指定します。 

1. **[geo 冗長]** で [いいえ] を選択します。 

   ![ポータルから復元する](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>PowerShell を使用してローカル冗長ストレージに専用 SQL プール (以前の SQL DW) を復元する
新しい専用 SQL プールを PowerShell で作成するには、[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。

1. 開始する前に、必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元するデータベースを含むサブスクリプションを選択します。
5. 専用 SQL プール (旧称 SQL DW) の復元ポイントを一覧表示します。
1. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。
1. Restore-AzSqlDatabase PowerShell コマンドレットを使用して、目的の復元ポイントに専用 SQL プール (以前の SQL DW) を復元します。BackupStorageRedundnacy には 'Local' を指定してください。 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>次のステップ

- [既存の専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [削除された専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-deleted-dw.md)
