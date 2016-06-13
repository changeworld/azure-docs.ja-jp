<properties
   pageTitle="Azure SQL Data Warehouse でのバックアップと復元 (PowerShell) | Microsoft Azure"
   description="Azure SQL Data Warehouse でライブ データベース、削除済みデータベース、またはアクセスできないデータベースを復元するための PowerShell のタスク。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL Data Warehouse でのデータベースのバックアップと復元 (PowerShell)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [ポータル](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

PowerShell を使用して SQL Data Warehouse でデータベースをバックアップおよび復元する方法。

このトピックのタスク:

- ライブ データベースの復元
- 削除されたデータベースの復元
- アクセスできないデータベースを地理的に異なる Azure リージョンから復元

[AZURE.INCLUDE [SQL Data Warehouse のバックアップ保持ポリシー](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 開始する前に

### SQL Database の DTU 容量を確認する 
SQL Data Warehouse では、論理 SQL サーバーの新しいデータベースに復元されるため、復元先 SQL サーバーに、新しいデータベース用の DTU 容量が十分にあることを確認することが重要です。詳細については、[DTU クォータを表示および増量する方法][]に関するブログ投稿記事をご覧ください。

### PowerShell をインストールする

SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell Version 1.0 以降をインストールする必要があります。**Get-Module -Name Azure -ListAvailable** を実行することで、バージョンを確認できます。最新バージョンは、[Microsoft Web プラットフォーム インストーラー][]からインストールできます。最新バージョンのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法][]」をご覧ください。

## ライブ データベースの復元

スナップショットからデータベースを復元するには、[Restore-AzureRmSqlDatabase][] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. データベースの復元ポイントを表示します。
5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。
6. 目的の復元ポイントに、データベースを復元します。
7. 復元されたデータベースがオンラインであることを確認します。

```PowerShell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] サーバーが foo.database.windows.net の場合、上記の PowerShell コマンドレットで -ServerName として "foo" を使用します。

復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。

## 削除されたデータベースの復元

削除済みデータベースを復元するには、[Restore-AzureRmSqlDatabase][] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元する削除済みデータベースを含むサブスクリプションを選択します。
4. 特定の削除済みデータベースを取得します。
5. その削除済みデータベースを復元します。
6. 復元されたデータベースがオンラインであることを確認します。

```PowerShell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] サーバーが foo.database.windows.net の場合、上記の PowerShell コマンドレットで -ServerName として "foo" を使用します。

復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。

## geo 冗長バックアップからのデータ ウェアハウスの geo リストア

データベースを復旧するには、[Restore-AzureRmSqlDatabase][] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. 復旧するデータベースを取得します。
5. データベースの復旧要求を作成します。
6. geo リストアされたデータベースの状態を確認します。

```PowerShell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### geo リストアの実行後にデータベースを構成する
復旧後のデータベースをすぐ運用できるようにするためのチェックリストです。

1. **接続文字列を更新する**: クライアント ツールの接続文字列が新しく復旧したデータベースを指していることを確認します。
2. **ファイアウォール規則を変更する**: ターゲット サーバーのファイアウォール規則を確認し、クライアント コンピューターまたは Azure からサーバーおよび新しく復旧されたデータベースへの接続が有効になっていることを確認します。
3. **サーバー ログインとデータベース ユーザーを確認する**: アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認します。不足しているログインを再作成し、復旧されたデータベースに対する適切なアクセス許可を与えます。 
4. **監査を有効にする**: データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: sql-database-business-continuity.md
[復旧されたデータベースの最終処理]: sql-database-recovered-finalize.md
[Azure PowerShell のインストールおよび構成方法]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[DTU クォータを表示および増量する方法]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web プラットフォーム インストーラー]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0601_2016-->