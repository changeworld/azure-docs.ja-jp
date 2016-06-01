<properties
   pageTitle="Azure SQL Data Warehouse の PowerShell コマンドレット"
   description="データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/14/2016"
   ms.author="sonyama;barbkess;mausher"/>

# SQL Data Warehouse の PowerShell コマンドレットと REST API

SQL Data Warehouse の管理タスクの多くは、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。以下に示す例では、PowerShell コマンドを使用して、SQL Data Warehouse で一般的なタスクを自動化する方法を示しています。同じ作業を自動化する REST API の一覧については、「[Azure SQL データベースの操作][]」をご覧ください。

> [AZURE.NOTE]  SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell Version 1.0.3 以降をインストールする必要があります。**Get-Module -ListAvailable -Name Azure** を実行することで、バージョンを確認できます。最新バージョンは、[Microsoft Web プラットフォーム インストーラー][]からインストールできます。最新バージョンのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法][]」をご覧ください。

## Azure PowerShell コマンドレットの使用開始

1. Windows PowerShell を開きます。 
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## SQL Data Warehouse の一時停止の例

"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを一時停止します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
バリエーションであるこの例では、取得したオブジェクトを [Suspend-AzureRmSqlDatabase][] にパイプ処理します。その結果、データベースが一時停止されます。最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## SQL Data Warehouse の開始の例

"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを再開します。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースを取得します。 取得したオブジェクトを [Suspend-AzureRmSqlDatabase][] にパイプ処理します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。

## よく使用される PowerShell コマンドレット

Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを次に示します。

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## 次のステップ
PowerShell のその他の例については、次のトピックをご覧ください。

- [PowerShell を使用して SQL Data Warehouse を作成する][]
- [SQL Data Warehouse でのデータベースのユーザー エラーからの復旧][]
- [SQL Data Warehouse でのデータベースの障害からの復旧][]

PowerShell で自動化できるタスクの一覧については、「[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット)][]」をご覧ください。

<!--Image references-->

<!--Article references-->
[Azure PowerShell のインストールおよび構成方法]: powershell-install-configure.md
[PowerShell を使用して SQL Data Warehouse を作成する]: sql-data-warehouse-get-started-provision-powershell.md
[SQL Data Warehouse でのデータベースのユーザー エラーからの復旧]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[SQL Data Warehouse でのデータベースの障害からの復旧]: sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md

<!--MSDN references-->
[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット)]: https://msdn.microsoft.com/library/mt574084.aspx
[Azure SQL データベースの操作]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web プラットフォーム インストーラー]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->