---
title: Azure SQL Data Warehouse を復元する | Microsoft Docs
description: Azure SQL Data Warehouse を復元する方法を説明します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 583346f2297f590d8e9484c0a3c19c947de7f740
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191500"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse を復元する 
この記事では次のことを行う方法を説明します。

- 復元ポイントを作成します
- 自動復元ポイントまたはユーザー定義の復元ポイントから復元します
- 削除されたデータベースから復元します
- geo バックアップから復元します
- ユーザー定義の復元ポイントからデータ ウェアハウスのコピーを作成します

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。  SQL データ ウェアハウスを復元する前に、データベースを復元するための十分な DTU クォータが SQL Server に残っていることを確認する必要があります。 必要な DTU を計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

# <a name="restore-through-powershell"></a>PowerShell を使用して復元する

## <a name="install-powershell"></a>PowerShell をインストールする
SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell Version 1.0 以降をインストールする必要があります。  **Get-Module -ListAvailable -Name AzureRM**を実行することで、バージョンを確認できます。  最新バージョンは、[Microsoft Web Platform Installer][Microsoft Web Platform Installer] からインストールできます。  最新バージョンのインストールの詳細については、[Azure PowerShell のインストールと構成方法][How to install and configure Azure PowerShell]に関するページを参照してください。

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
復元ポイントからデータベースを復元するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell コマンドレットを使用します。

1. Windows PowerShell を開きます。

2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。

3. 復元するデータベースを含むサブスクリプションを選択します。

4. データベースの復元ポイントを表示します。

5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。

   > [!NOTE]
   > 復元するときに、異なる ServiceObjectiveName (DWU) や、異なるリージョンに存在する別のサーバーを指定できます。

6. 目的の復元ポイントに、データベースを復元します。

7. 復元されたデータベースがオンラインであることを確認します。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points"></a>ユーザー定義の復元ポイントでデータ ウェアハウスをコピーする
ユーザー定義の復元ポイントからデータベースを復元するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. データベースの即時コピーに対する復元ポイントを作成します。
5. データベースの名前を一時的な名前に変更します。
5. 指定した RestorePointLabel で最新の復元ポイントを取得します。
6. 復元を開始するデータベースのリソース ID を取得します。
6. 目的の復元ポイントに、データベースを復元します。
7. 復元されたデータベースがオンラインであることを確認します。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
削除されたデータベースを復元するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元する削除済みデータベースを含むサブスクリプションを選択します。
4. 特定の削除済みデータベースを取得します。
5. その削除済みデータベースを復元します。
6. 復元されたデータベースがオンラインであることを確認します。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
>

## <a name="restore-from-an-azure-geographical-region"></a>Azure 地理的リージョンからの復元
データベースを復旧するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] コマンドレットを使用します。

> [!NOTE]
> Gen2 への geo リストアを行うことができます。 そのためには、省略可能なパラメーターとして Gen2 の ServiceObjectiveName (例: DW1000**c**) を指定します。
>

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. 復旧するデータベースを取得します。
5. データベースの復旧要求を作成します。
6. geo リストアされたデータベースの状態を確認します。

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、「[復旧後のデータベースの構成][Configure your database after recovery]」をご覧ください。
>

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。

# <a name="restore-through-the-azure-portal"></a>Azure portal を使用して復元する

## <a name="create-a-user-defined-restore-point"></a>ユーザー定義の復元ポイントを作成する
1. [Azure Portal][Azure portal] にサインインします。

2. 復元ポイントを作成する対象の SQL データ ウェアハウスに移動します。

3. [概要] ブレードの上部にある **[+ 新しい復元ポイント]** を選択します。

    ![新しい復元ポイント](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)
    
4. 復元ポイントの名前を指定します。

    ![復元ポイントの名前](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
1. [Azure Portal][Azure portal] にサインインします。
2. 復元元の SQL データ ウェアハウスに移動します。
3. [概要] ブレードの上部にある **[復元]** を選択します。

    ![ 復元の概要](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)
    
4. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。

    ![自動復元ポイント](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)
    
5. ユーザー定義の復元ポイントの場合は、**復元ポイントを選択**するか、または**新しいユーザー定義の復元ポイントを作成**します。

    ![ユーザー定義の復元ポイント](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
1. [Azure Portal][Azure portal] にサインインします。
2. 削除されたデータベースがホストされていた SQL Server に移動します。
3. 目次で [Deleted databases]\(削除済みデータベース\) アイコンを選択します。

    ![削除済みデータベース](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)
    
4. 復元する削除済みデータベースを選択します。

    ![削除済みデータベースを選択する](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)
    
5. 新しいデータベース名を指定します。

    ![データベース名を指定する](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
