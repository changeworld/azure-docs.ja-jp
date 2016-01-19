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
   ms.date="01/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell を使用して SQL Data Warehouse を作成する

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]SQL Data Warehouse で Microsoft Azure Powershell を使用するには、バージョン 1.0 以降が必要です。PowerShell で (Get-Module Azure).Version を実行することで、バージョンを確認できます。

## Azure PowerShell コマンドレットの取得と実行
PowerShell をまだセットアップしていない場合は、ダウンロードして構成する必要があります。

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。
2. モジュールを実行するには、スタート ウィンドウに「**Microsoft Azure PowerShell**」と入力します。
3. コンピューターにアカウントをまだ追加していない場合は、次のコマンドレットを実行します (詳細については、「[Azure PowerShell のインストールと構成の方法][]」を参照してください)。

```
Add-AzureAccount
```

4. 使用するサブスクリプションを選択します。この例では、サブスクリプション名の一覧を取得します。次に、サブスクリプション名を "MySubscription" に設定します。 

```
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```
   
## SQL Data Warehouse の作成
PowerShell が自分のアカウント用に構成されたら、以下のコマンドレットを実行して、SQL Data Warehouse で新しいデータベースをデプロイできます。

```
New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

このコマンドレットに必要なパラメーターは次のとおりです。

 + **RequestedServiceObjectiveName**: 要求する DWU の量。"DWXXX" の形式です。現在サポートされている値は、100、200、300、400、500、600、1000、1200、1500、2000 です。
 + **DatabaseName**: 作成する SQL Data Warehouse の名前。
 + **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
 + **ResourceGroupName**: 使用するリソース グループ。サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
 + **Edition**: SQL Data Warehouse を作成するには、エディションを "DataWarehouse" に設定する必要があります。 

コマンド リファレンスについては、「[New-azuresqldatabase](https://msdn.microsoft.com/library/mt619339.aspx)」を参照してください。

パラメーターのオプションについては、「[Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx)」を参照してください。

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了したら、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を調べたりできます。

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

<!---HONumber=AcomDC_0114_2016-->