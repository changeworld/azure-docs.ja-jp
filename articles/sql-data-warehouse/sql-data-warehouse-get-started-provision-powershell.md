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
   ms.date="01/04/2016"
   ms.author="lodipalm"/>

# Powershell を使用して SQL Data Warehouse を作成する

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

>  [AZURE.NOTE]SQL Data Warehouse で Microsoft Azure Powershell を使用するには、バージョン 0.9.4 以降が必要です。Powershell で (Get-Module Azure).Version を実行することで、バージョンを確認できます。

## Azure PowerShell コマンドレットの取得と実行
Powershell をまだセットアップしていない場合は、次のようにします。

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。
2. モジュールを実行するには、スタート ウィンドウに「**Microsoft Azure PowerShell**」と入力します。
3. コンピューターにアカウントをまだ追加していない場合は、次のコマンドレットを実行します (詳細については、「[Azure PowerShell のインストールと構成の方法][]」を参照してください)。

            Add-AzureAccount

4. ARM モードで PowerShell を実行する必要もあります。このモードに切り替えるには、次のコマンドを実行します。

            switch-azuremode AzureResourceManager

## SQL Data Warehouse の作成
アカウントで Powershell をセットアップした後は、次のコマンドを実行して新しい SQL Data Wareouse をデプロイできます。

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

このコマンドレットに必要なパラメーターは次のとおりです。

 + **RequestedServiceObjectiveName**: 要求する DWU の量。"DWXXX" の形式です。現在サポートされている値は、100、200、300、400、500、600、1000、1200、1500、2000 です。
 + **DatabaseName**: 作成する SQL Data Warehouse の名前。
 + **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
 + **ResourceGroupName**: 使用するリソース グループ。サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
 + **Edition**: SQL Data Warehouse を作成するには、エディションを "DataWarehouse" に設定する必要があります。 

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を確認したりできます。

プログラムでの SQL Data Warehouse の管理方法について詳しくは、[Powershell][] または [REST API][] のドキュメントを参照してください。



<!--Image references-->

<!--Article references-->
[移行]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-overview-migrate/
[開発]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-overview-develop/
[読み込み]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-overview-load/
[サンプル データを読み込んだり]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-database-configure-firewall-settings/
[Azure PowerShell のインストールと構成の方法]: powershell-install-configure.md

<!---HONumber=AcomDC_0107_2016-->