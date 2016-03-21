<properties
   pageTitle="Powershell を使用して SQL Data Warehouse を作成する |Microsoft Azure"
   description="Powershell を使用して SQL Data Warehouse を作成します"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell を使用して SQL Data Warehouse を作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Azure PowerShell コマンドレットの取得と実行

> [AZURE.NOTE]  Microsoft Azure PowerShell を SQL Data Warehouse と共に使用するには、ARM コマンドレットを使用して Azure PowerShell の最新バージョンをダウンロードしてインストールする必要があります。`Get-Module -ListAvailable -Name Azure` を実行することで、ご使用のバージョンを確認できます。この記事は、Microsoft Azure PowerShell バージョン 1.0.3 に基づいています。

PowerShell をまだセットアップしていない場合は、ダウンロードして構成する必要があります。

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。
2. モジュールを実行するには、スタート ウィンドウに「**Windows PowerShell**」と入力します。
3. このコマンドレットを実行して、Azure リソース マネージャーにログインします。詳細については、「[Azure PowerShell のインストールと構成の方法][]」を参照してください。

	```
	Login-AzureRmAccount
	```

4. 現在のセッションに使用するサブスクリプションを選択します。

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## SQL Data Warehouse データベースを作成する
SQL Data Warehouse をデプロイするには、New-AzureRmSQLDatabase コマンドレットを使用します。コマンドを実行する前に、次の前提条件を満たしていることを確認してください。

### 前提条件

- データベースをホストする V12 Azure SQL Server
- SQL Server のリソース グループ名を確認します。

### デプロイメント コマンド

このコマンドは、SQL Data Warehouse に新しいデータベースをデプロイします。

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

この例では、サービス目標レベル "DW400" で "mynewsqldw1" という名前の新しいデータベースを、"mywesteuroperesgp1" という名前のリソース グループ内の "sqldwserver1" という名前のサーバーにデプロイします。

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

このコマンドレットに必要なパラメーターは次のとおりです。

 + **RequestedServiceObjectiveName**: 要求する DWU の量。"DWXXX" の形式です。現在サポートされている値は、100、200、300、400、500、600、1000、1200、1500、2000 です。
 + **DatabaseName**: 作成する SQL Data Warehouse の名前。
 + **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
 + **ResourceGroupName**: 使用するリソース グループ。サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
 + **Edition**: SQL Data Warehouse を作成するには、エディションを "DataWarehouse" に設定する必要があります。

コマンド リファレンスについては、「[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx)」を参照してください。

パラメーターのオプションについては、「[Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx)」を参照してください。

## 次のステップ
SQL Data Warehouse のプロビジョニングが済めば、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を調べたりできます。

プログラムでの SQL Data Warehouse の管理方法について詳しくは、[Powershell][] または [REST API][] のドキュメントを参照してください。



<!--Image references-->

<!--Article references-->
[移行]: ./sql-data-warehouse-overview-migrate.md
[開発]: ./sql-data-warehouse-overview-develop/.md
[サンプル データを読み込んだり]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Azure PowerShell のインストールと構成の方法]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0309_2016-->