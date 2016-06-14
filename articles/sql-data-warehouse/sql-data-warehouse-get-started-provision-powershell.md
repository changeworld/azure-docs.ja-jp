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
   ms.date="06/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell を使用して SQL Data Warehouse を作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## 前提条件
開始する前に、以下の前提条件を確認します。

- **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][]に関するページまたは [MSDN Azure クレジット][]に関するページを参照してください。
- **V12 Azure SQL Server**: [Azure ポータルを使用した Azure SQL Database 論理サーバーの作成][]に関する記事または [PowerShell を使用した Azure SQL Database 論理サーバーの作成][]に関する記事を参照してください。
- **リソース グループ名**: V12 Azure SQL Server と同じリソース グループを使用します。新しいリソース グループを作成する場合は、[リソース グループ][]に関する記事を参照してください。
- **PowerShell バージョン 1.0.3 以降**: **Get-Module -ListAvailable -Name Azure** を実行することで、バージョンを確認できます。最新バージョンは、[Microsoft Web Platform Installer][] からインストールできます。最新バージョンのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法][]」を参照してください。

> [AZURE.NOTE] 新しい SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。料金の詳細については、「[SQL Data Warehouse の価格][]」を参照してください。

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

4.  データベースを作成します。この例では、サービス目標レベル "DW400" で "mynewsqldw" という名前の新しいデータベースを、"mywesteuroperesgp1" という名前のリソース グループ内の "sqldwserver1" という名前のサーバーに作成します。

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

このコマンドレットに必要なパラメーターは、次のとおりです。

- **RequestedServiceObjectiveName**: 要求する DWU の量。"DWXXX" の形式です。DWU は、CPU とメモリの割り当てを表します。各 DWU 値は、これらのリソースの線形増加を表しています。現在サポートされている値は、100、200、300、400、500、600、1000、1200、1500、2000 です。
- **DatabaseName**: 作成する SQL Data Warehouse の名前。
- **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
- **ResourceGroupName**: 使用するリソース グループ。サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
- **Edition**: SQL Data Warehouse を作成するには、エディションを "DataWarehouse" に設定する必要があります。

パラメーターのオプションの詳細については、「[CREATE DATABASE (Azure SQL Data Warehouse)][]」を参照してください。コマンド リファレンスについては、「[New-AzureRmSqlDatabase][]」を参照してください。

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を確認したりできます。

プログラムで SQL Data Warehouse を管理する方法の詳細については、[PowerShell コマンドレットと REST API][] の使用方法に関する記事を参照してください。

<!--Image references-->

<!--Article references-->

[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[読み込み]: sql-data-warehouse-load-with-bcp.md
[サンプル データを読み込んだり]: sql-data-warehouse-get-started-load-sample-databases.md
[PowerShell コマンドレットと REST API]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Azure PowerShell のインストールおよび構成方法]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Azure ポータルを使用した Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[PowerShell を使用した Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[リソース グループ]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[CREATE DATABASE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 無料試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure クレジット]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0608_2016-->