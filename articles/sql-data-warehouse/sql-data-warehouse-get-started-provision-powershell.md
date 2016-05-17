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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell を使用して SQL Data Warehouse を作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

### 前提条件
開始する前に、以下の前提条件を確認します。

- データベースをホストする V12 Azure SQL Server
- SQL Server のリソース グループ名

上の前提条件の詳細については、[Azure ポータルから SQL Data Warehouse を作成する方法][]に関する記事の「**サーバーを構成して作成する**」を参照してください。

> [AZURE.NOTE]  SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell Version 1.0.3 以降をインストールする必要があります。**Get-Module -ListAvailable -Name Azure** を実行することで、バージョンを確認できます。最新バージョンは、[Microsoft Web プラットフォーム インストーラー][]からインストールすることができます。最新バージョンのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法][]」を参照してください。

## SQL Data Warehouse データベースを作成する
1. Windows PowerShell を開きます。
2. このコマンドレットを実行して、Azure リソース マネージャーにログインします。

	```Powershell
	Login-AzureRmAccount
	```
	
3. 現在のセッションに使用するサブスクリプションを選択します。

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  データベースを作成します。この例では、サービス目標レベル "DW400" で "mynewsqldw" という名前の新しいデータベースを、"mywesteuroperesgp1" という名前のリソース グループ内の "sqldwserver1" という名前のサーバーに作成します。**注: 新しい SQL Data Warehouse データベースを作成すると、新たに請求対象の課金が生じる場合があります。料金の詳細については、「[SQL Data Warehouse の価格][]」を参照してください。**

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

このコマンドレットに必要なパラメーターは、次のとおりです。

- **RequestedServiceObjectiveName**: 要求する DWU の量。"DWXXX" の形式です。DWU は、CPU とメモリの割り当てを表します。各 DWU 値は、これらのリソースの線形増加を表しています。現在サポートされている値は、100、200、300、400、500、600、1000、1200、1500、2000 です。
- **DatabaseName**: 作成する SQL Data Warehouse の名前。
- **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
- **ResourceGroupName**: 使用するリソース グループ。サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
- **Edition**: SQL Data Warehouse を作成するには、エディションを "DataWarehouse" に設定する必要があります。

パラメーターのオプションの詳細については、「[Create Database (Azure SQL Data Warehouse)][]」を参照してください。コマンド リファレンスについては、「[New-AzureRmSqlDatabase][]」を参照してください。

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を確認したりできます。

プログラムでの SQL Data Warehouse の管理方法について詳しくは、[PowerShell コマンドレットと REST API][] の使用方法に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[読み込み]: sql-data-warehouse-load-with-bcp.md
[サンプル データを読み込んだり]: sql-data-warehouse-get-started-manually-load-samples.md
[PowerShell コマンドレットと REST API]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: sql-database-configure-firewall-settings.md
[Azure PowerShell のインストールおよび構成方法]: ../powershell/powershell-install-configure.md
[Azure ポータルから SQL Data Warehouse を作成する方法]: sql-data-warehouse-get-started-provision.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web プラットフォーム インストーラー]: https://aka.ms/webpi-azps
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
 

<!---HONumber=AcomDC_0511_2016-->