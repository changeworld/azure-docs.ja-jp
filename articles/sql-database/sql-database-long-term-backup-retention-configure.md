---
title: Azure SQL Database の長期的なバックアップ保有期間を管理する | Microsoft Docs
description: 自動バックアップを SQL Azure ストレージに保存して復元する方法について説明します
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6f0d8a3a09ce000ddff078614c4febfc44ac941f
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264945"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database の長期的なバックアップ保有期間を管理する

[長期的なバックアップ保有期間](sql-database-long-term-retention.md)ポリシー (LTR) を使用して Azure SQL Database を構成し、Azure BLOB ストレージに最大 10 年間自動的にバックアップを保持することができます。 Azure Portal または PowerShell でこのようなバックアップを使用して、データベースを復旧できます。

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Azure Portal を使用して長期保存ポリシーを構成し、バックアップを復元する

以下のセクションでは、Azure Portal を使用して長期保存を構成し、長期保存のバックアップを表示し、長期保存からバックアップを復元する方法について説明します。

### <a name="configure-long-term-retention-policies"></a>長期保存ポリシーを構成する

ご利用のサービス レベルのリテンション期間より長く[自動バックアップを保持](sql-database-long-term-retention.md)するように SQL Database を構成できます。 

1. Azure Portal で SQL Server を選択し、**[バックアップの管理]** をクリックします。 **[ポリシーの構成**] タブで、長期的なバックアップ保有期間ポリシーを設定または変更するデータベースを選択します。

   ![バックアップの管理リンク](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. **[ポリシーの構成]** ウィンドウで、毎週、毎月、または毎年のバックアップを保持するかどうかを選択し、それぞれの保有期間を指定します。 

   ![ポリシーを構成する](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. 完了したら、**[適用]** をクリックします。

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Azure Portal を使用してバックアップを表示し、バックアップから復元する

LTR ポリシーを使用して保持されている特定のデータベースのバックアップを表示し、それらのバックアップから復元します。 

1. Azure Portal で SQL Server を選択し、**[バックアップの管理]** をクリックします。 **[利用可能なバックアップ]** タブで、利用可能なバックアップを表示するデータベースを選択します。

   ![データベースを選択する](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. **[利用可能なバックアップ]** ウィンドウで、利用可能なバックアップを確認します。 

   ![バックアップを確認する](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. 復元するバックアップを選択し、新しいデータベース名を指定します。

   ![復元](./media/sql-database-long-term-retention/ltr-restore.png)

5. **[OK]** をクリックして、Azure SQL ストレージ内にあるバックアップから新しいデータベースにデータベースを復元します。

6. ツール バーの通知アイコンをクリックして、復元ジョブの状態を確認します。

   ![復元ジョブの進行状況](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 復元ジョブが完了したら、**[SQL データベース]** ページを開き、新しく復元されたデータベースを確認します。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](sql-database-recovery-using-backups.md#point-in-time-restore)できます。
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>PowerShell を使用して長期的な保有期間ポリシーを構成し、バックアップを復元する

以下のセクションでは、PowerShell を使用して長期的なバックアップ保有期間を構成し、Azure SQL ストレージ内のバックアップを表示し、Azure SQL ストレージ内のバックアップから復元する方法について説明します。

> [!IMPORTANT]
> LTR V2 API は、次の PowerShell のバージョンでサポートされています。
- [AzureRM.Sql-4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) 以降
- [AzureRM-6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) 以降
> 

### <a name="create-an-ltr-policy"></a>LTR ポリシーを作成する

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR ポリシーを表示する
サーバー内の LTR ポリシーを一覧表示する例を次に示します

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>LTR ポリシーをクリアする
LTR ポリシーをデータベースから消去する例を次に示します

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR バックアップを表示する

サーバー内の LTR バックアップを一覧表示する例を次に示します。 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR バックアップを削除する

バックアップの一覧から LTR バックアップを削除する例を次に示します。

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する
LTR バックアップから復元する例を次に示します。 このインターフェイスは変更されませんでしたが、リソース ID パラメーターでは LTR バックアップ リソース ID が必須になりました。 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したりできます。 [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)をご覧ください。

## <a name="next-steps"></a>次の手順

- サービスによって生成された自動バックアップについては、[自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
