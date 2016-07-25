<properties 
	pageTitle="PowerShell を使用した Azure SQL Database の管理" 
	description="PowerShell による Azure SQL Database 管理。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="sstein"/>

# PowerShell を使用した Azure SQL Database の管理


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

このトピックでは、多数の Azure SQL Database タスクを実行する PowerShell コマンドについて説明します。

[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]


## リソース グループの作成

サーバーを含むリソース グループを作成します。任意の有効な場所を使用するには、次のコマンドを編集します。

有効な Azure SQL Database サーバーの場所一覧については、次のコマンドレットを実行します。

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

既にリソース グループがある場合、サーバーの作成に進みます。次のコマンドを実行して新しいリソース グループを作ることもできます。

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## サーバーの作成 

新しい V12 サーバーを作成するには、[New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) コマンドレットを使用します。server12 をご利用のサーバー名に置き換えます。サーバー名が既に使われている場合はエラーが発生する可能性があるため、Azure SQL Server で一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。サーバーが正常に作成されると、サーバーの詳細と PowerShell のプロンプトが表示されます。コマンドを編集すると、任意の有効な場所を使用できます。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

## サーバー ファイアウォール規則の作成

サーバーにアクセスするためのファイアウォール規則を作成するには、[New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) コマンドを使用します。ご利用のクライアントで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

サーバーで別の Azure サービスへのアクセス許可が必要な場合は、**- AllowAllAzureIPs** スイッチを追加して特別なファイアウォール規則を追加し、サーバーへのすべての Azure トラフィック アクセスを許可します。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。

## SQL Database の作成

データベースを作成するには、[New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドを使用します。データベースを作成するには、サーバーが必要です。次の例では TestDB12 という名前の SQL Database を作成します。このデータベースは、Standard S1 データベースとして作成されます。

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## SQL Database のパフォーマンス レベルを変更します

[Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドを使用してデータベースをスケールアップまたはスケールダウンできます。次の例では、TestDB12 という名前の SQL Database を、現在のパフォーマンス レベルから Standard S3 レベルにスケールアップします。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## SQL Database の削除

[Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) コマンドを使用して SQL Database を削除できます。次の例では TestDB12 という名前の SQL Database を削除します。

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## サーバーの削除

サーバーは、[Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) コマンドを使用して削除することもできます。次の例では server12 という名前のサーバーを削除します。


>[AZURE.NOTE]  削除操作は非同期であり、時間がかかる場合があるため、サーバーが完全に削除されたことを前提とする追加の操作 (たとえば、同じ名前の新しいサーバーの作成) を実行する前に、操作が完了したことを確認してください。


	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"




## 次のステップ

コマンドを結合し、自動化します。たとえば、サーバー、ファイアウォール規則、およびデータベースを作成するために、< および > 文字を含む引用符内のすべてのものを置換します。


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 関連情報

- [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0713_2016-->