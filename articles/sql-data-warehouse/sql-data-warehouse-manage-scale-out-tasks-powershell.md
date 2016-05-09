<properties
   pageTitle="Azure SQL Data Warehouse のコンピューティング リソースをスケール アウトするためのタスク | Microsoft Azure"
   description="Azure SQL Data Warehouse データベースのコンピューティング リソースを一時停止 (中断) または開始 (再開) する方法、およびサービス レベル目標 (SLO) の DWU 設定をスケール アウトまたはスケール バックする方法について確認してください。これらのタスクでは、Azure PowerShell コマンドレットが使用されます。"
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
   ms.date="04/21/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のコンピューティング リソースをスケール アウトまたはスケール バックするためのタスク

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)


コンピューティング リソースとメモリを、ワークロードの変化する需要に合わせて柔軟にスケール アウトし、ピーク時以外にはリソースをスケール バックしてコストを削減します。

この一連のタスクでは、次の処理を行うのに PowerShell コマンドレットを使用します。

- コンピューティング リソースの一時停止
- コンピューティング リソースの再開
- DWU を調整することによるコンピューティング リソースの変更

## 開始する前に

### Azure PowerShell の最新バージョンのインストール

> [AZURE.NOTE]  SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell バージョン 1.0.3 以降が必要です。現在のバージョンを確認するには、コマンド **Get-module-ListAvailable-Name** を実行します。最新バージョンは、"Microsoft Web プラットフォーム インストーラー" からインストールすることができます。詳細については、「Azure PowerShell のインストールと構成の方法」を参照してください。

### Azure PowerShell コマンドレットの使用開始

作業を開始するには:

1. Azure PowerShell を開きます。 
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## タスク 1: コンピューティングの一時停止

[AZURE.INCLUDE [SQL Data Warehouse の一時停止の説明](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、[Suspend-AzureRmSqlDatabase][] コマンドレットを使用します。次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

> [AZURE.NOTE] サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。オブジェクトは [Suspend-AzureRmSqlDatabase][] にパイプ処理されます。結果は、オブジェクト resultDatabase に格納されます。最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## タスク 2: コンピューティングの再開

[AZURE.INCLUDE [SQL Data Warehouse の再開の説明](../../includes/sql-data-warehouse-resume-description.md)]

データベースを開始するには、[Resume-AzureRmSqlDatabase][] コマンドレットを使用します。次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが開始されます。サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。オブジェクトは [Resume-AzureRmSqlDatabase][] でパイプ処理され、結果は $resultDatabase に格納されます。最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```


## タスク 3: DWU のスケーリング

[AZURE.INCLUDE [SQL Data Warehouse の DWU のスケーリングの説明](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU を変更するには、 [Set-AzureRmSqlDatabase][] PowerShell コマンドレットを使用します。次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

## 次のステップ

他の管理タスクについては、「[管理の概要][]」を参照してください。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理の概要]: ./sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0427_2016-->