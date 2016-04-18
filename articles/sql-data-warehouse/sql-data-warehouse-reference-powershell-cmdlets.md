<properties
   pageTitle="Azure SQL Data Warehouse の PowerShell コマンドレット"
   description="データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="barbkess;mausher;sonyama"/>

# SQL Data Warehouse の PowerShell コマンドレットと REST API

SQL Data Warehouse は、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。

**Azure SQL Database** 用に定義されているコマンドのほとんどは、**SQL Data Warehouse** でも使用されます。最新のリストについては、「[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/mt574084.aspx)」を参照してください。コマンドレット [Suspend-AzureRmSqlDatabase][] と [Resume-AzureRmSqlDatabase][] は、SQL Data Warehouse 用に追加作成されたものです。

同様に、**SQL Azure Database** 用の REST API も **SQL Data Warehouse** インスタンスに使用できます。最新のリストについては、「[Azure SQL データベースの操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)」を参照してください。

## Azure PowerShell コマンドレットの使用開始

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://aka.ms/webpi-azps) を実行します。このインストーラーの詳細については、「[Azure PowerShell のインストールおよび構成方法][]」をご覧ください。
2. PowerShell を起動するには、**[スタート]** をクリックし、「**Windows PowerShell**」と入力します。
3. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## 頻繁に使用される PowerShell コマンドレット

次の PowerShell コマンドレットは、Azure SQL Data Warehouse で頻繁に使用されます。


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


## SQL Data Warehouse の例

SQL Data Warehouse にのみ適用される機能の例を以下に示します。

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## 次のステップ
詳細な参照情報については、[SQL Data Warehouse のリファレンス概要][]に関するページを参照してください。PowerShell のその他の例については、次のトピックをご覧ください。
- [PowerShell を使用して SQL Data Warehouse を作成する](sql-data-warehouse-get-started-provision-powershell.md)
- [SQL Data Warehouse でのデータベースのユーザー エラーからの復旧](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [SQL Data Warehouse でのデータベースの障害からの復旧](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse のリファレンス概要]: sql-data-warehouse-overview-reference.md
[Azure PowerShell のインストールおよび構成方法]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0406_2016-->