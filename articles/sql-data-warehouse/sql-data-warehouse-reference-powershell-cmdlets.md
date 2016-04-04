<properties
   pageTitle="SQL Data Warehouse での PowerShell コマンドレットと REST API の使用"
   description="PowerShell コマンドレットを使用した SQL Data Warehouse の一時停止と再開"
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
   ms.date="03/03/2016"
   ms.author="barbkess;mausher;sonyama"/>

# SQL Data Warehouse での PowerShell コマンドレットと REST API の使用

SQL Data Warehouse は、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。

**Azure SQL Database** に対して定義されているコマンドは、**SQL Data Warehouse** に対しても使用されます。最新のリストについては、「[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/mt574084.aspx)」を参照してください。コマンドレット **Suspend-AzureRmSqlDatabase** と **Resume-AzureRmSqlDatabase** (以下参照) は、SQL Data Warehouse 用に追加作成されたものです。

同様に、**SQL Azure Database** 用の REST API も **SQL Data Warehouse** インスタンスに使用できます。最新のリストについては、「[Azure SQL データベースの操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)」を参照してください。

## Azure PowerShell コマンドレットの取得と実行

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。
2. モジュールを実行するには、スタート ウィンドウに「**Windows PowerShell**」と入力します。
3. コンピューターにアカウントをまだ追加していない場合は、次のコマンドレットを実行します (詳細については、「[Azure PowerShell のインストールおよび構成方法]()」を参照してください)。

	```
	Login-AzureRmAccount
	```

3. 中断または再開するデータベースのサブスクリプションを選択します。次の例では、"MySubscription" という名前のサブスクリプションが選択されます。

	```
	Select-AzureRmSubscription -SubscriptionName "MySubscription"
	```

## Suspend-AzureRmSqlDatabase

コマンド リファレンスについては、「[Suspend-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619337.aspx)」を参照してください。

### 例 1: サーバー上で名前によってデータベースを一時停止する

この例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

### 例 2: データベース オブジェクトを一時停止する

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 取得したオブジェクトは **Suspend-AzureRmSqlDatabase** にパイプ処理されます。その結果、データベースが一時停止されます。最後のコマンドは結果を表示します。

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Resume-AzureSqlDatabase

コマンド リファレンスについては、「[Resume-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619347.aspx)」を参照してください。

### 例 1: サーバー上で名前によってデータベースを再開する

この例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが再開されます。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

```
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

### 例 2: データベース オブジェクトの再開

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 オブジェクトは **Resume-AzureRmSqlDatabase** にパイプ処理されます。

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

## Get-AzureRmSqlDatabaseRestorePoints

このコマンドレットは、Azure SQL Data Warehouse データベースのバックアップ復元ポイントを一覧表示します。復元ポイントは、データベースの復元に使用されます。返されるオブジェクトのプロパティは、次のとおりです。

プロパティ|説明
---|---
RestorePointType|DISCRETE / CONTINUOUS.不連続復元ポイントは、Azure SQL Data Warehouse データベースを復元できる特定の時点を示します。連続復元ポイントは、Azure SQL Database を復元できる最も早い時点を示します。データベースは、最も早い時点以降の任意の時点に復元できます。
EarliestRestoreDate|最も早い復元時間 (restorePointType = CONTINUOUS のときに入力)
RestorePointCreationDate |バックアップのスナップショット時間 (restorePointType = DISCRETE のときに入力)

### 例 1: サーバー上で名前によってデータベースの復元ポイントを取得する
この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースの復元ポイントが取得されます。

```
$restorePoints = Get-AzureRmSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints
```


### 例 2: データベース オブジェクトの再開

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 データベース オブジェクトは **Get-AzureRmSqlDatabase** にパイプ処理され、データベースの復元ポイントが結果として得られます。最後のコマンドは結果を出力します。

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints = $database | Get-AzureRmSqlDatabaseRestorePoints
$retorePoints
```


> [AZURE.NOTE] サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。


## 次のステップ
詳細な参照情報については、[SQL Data Warehouse のリファレンス概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse のリファレンス概要]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0309_2016-->