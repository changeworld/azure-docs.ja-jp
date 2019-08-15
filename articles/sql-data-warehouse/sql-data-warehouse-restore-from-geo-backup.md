---
title: geo バックアップからの Azure SQL Data Warehouse の復元 | Microsoft Docs
description: Azure SQL Data Warehouse を geo 復元する方法を説明します。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68425878"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の geo 復元

この記事では、Azure portal と PowerShell を使用して geo バックアップからデータ ウェアハウスを復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。 データベースを復元するための十分な DTU クォータが SQL Server に残っていることを確認する必要があります。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell を使用して Azure 地理的リージョンから復元する

Geo バックアップから復元するには、[Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] および [Restore-AzSqlDatabase][Restore-AzSqlDatabase] コマンドレットを使用します。

> [!NOTE]
> Gen2 への geo リストアを行うことができます。 そのためには、省略可能なパラメーターとして Gen2 の ServiceObjectiveName (例: DW1000**c**) を指定します。
>

1. 開始する前に、必ず [Azure PowerShell をインストール][Install Azure PowerShell]してください。
2. PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータ ウェアハウスを含むサブスクリプションを選択します。
4. 復旧するデータ ウェアハウスを取得します。
5. データ ウェアハウスの復旧要求を作成します。
6. geo リストアされたデータ ウェアハウスの状態を確認します。
7. 復元が完了した後にデータ ウェアハウスを構成する方法については、「[復旧後のデータベースの構成][Configure your database after recovery]」を参照してください。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure portal を使用して Azure 地理的リージョンから復元する

Azure SQL Data Warehouse を geo バックアップから復元するには、次の手順に従います。

1. [Azure portal][Azure portal] アカウントにサインインします。
1. **[+ Create a resource]\(+ リソースの作成\)** をクリックし、SQL Data Warehouse を検索して、 **[作成]** をクリックします。

    ![新しい DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. **[基本]** タブで要求された情報を入力し、 **[次へ: 追加設定]** をクリックします。

    ![基本](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. **[既存のデータを使用します]** パラメーターで、 **[バックアップ]** を選択し、スクロール ダウン オプションから適切なバックアップを選択します。 **[Review + Create]\(レビュー + 作成\)** をクリックします。
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. データ ウェアハウスが復元されたら、 **[状態]** がオンラインになっていることを確認します。

## <a name="next-steps"></a>次の手順
- [既存のデータ ウェアハウスを復元する][Restore an existing data warehouse]
- [削除されたデータ ウェアハウスを復元する][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
