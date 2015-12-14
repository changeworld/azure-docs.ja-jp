<properties 
	pageTitle="PowerShell を使用した Azure SQL Database の管理" 
	description="PowerShell を使用した Azure SQL Database の管理。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="sstein"/>

# PowerShell を使用した Azure SQL Database の管理


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

このトピックでは、多数の Azure SQL Database タスクを実行する PowerShell コマンドについて説明します。


> [AZURE.IMPORTANT]Azure PowerShell 1.0 Preview のリリースから、Switch-AzureMode コマンドレットは利用できなくなりました。また、Azure ResourceManger モジュールで使用されていたコマンドレットは名前が変更されました。この記事の例では、新しい PowerShell 1.0 Preview の名付け規則が使用されています。詳細については、[Azure PowerShell での Switch-AzureMode の廃止](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)に関するページを参照してください。


PowerShell コマンドレットを実行するには、Azure PowerShell をインストールして実行する必要があります。Switch-AzureMode が削除されたため、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行し、最新の Azure PowerShell をダウンロードしてインストールする必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。



## 資格情報の構成

Azure サブスクリプションに PowerShell コマンドレットを実行するには、Azure アカウントにまずアクセスできるようにする必要があります。次を実行すると資格情報を入力するサインイン画面が表示されます。Azure クラシック ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


## Azure サブスクリプションを選択します。

使用するサブスクリプションを選択するには、サブスクリプション ID (**SubscriptionId**) またはサブスクリプション名 (**SubscriptionName**) が必要になります。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。

現在のサブスクリプションを設定するには、サブスクリプション情報と共に次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

上で選択したサブスクリプションに対し、次のコマンドが実行されます。

## リソース グループの作成

サーバーを含むリソース グループを作成します。任意の有効な場所を使用するには、次のコマンドを編集します。

有効な Azure SQL Database サーバーの場所一覧については、次のコマンドレットを実行します。

	$AzureSQLLocations = Get-AzureRMLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

既にリソース グループがある場合、サーバーの作成に進みます。次のコマンドを実行して新しいリソース グループを作ることもできます。

	New-AzureRMResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## サーバーの作成 

新しい V12 サーバーを作成するには、[New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) コマンドレットを使用します。server12 をご利用のサーバー名に置き換えます。サーバー名が既に使われている場合はエラーが発生する可能性があるため、Azure SQL Server で一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。サーバーが正常に作成されると、サーバーの詳細と PowerShell のプロンプトが表示されます。コマンドを編集すると、任意の有効な場所を使用できます。

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

## サーバー ファイアウォール規則の作成

サーバーにアクセスするためのファイアウォール ルールを作成するには、[New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) コマンドを使用します。ご利用のクライアントで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

サーバーで別の Azure サービスへのアクセス許可が必要な場合は、**- AllowAllAzureIPs** スイッチを追加して特別なファイアウォール規則を追加し、サーバーへのすべての Azure トラフィック アクセスを許可します。

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。

## SQL Database の作成

データベースを作成するには、[New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドを使用します。データベースを作成するには、サーバーが必要です。次の例では TestDB12 という名前の SQL Database を作成します。このデータベースは、Standard S1 データベースとして作成されます。

	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## SQL Database のパフォーマンス レベルを変更します

[Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドを使用してデータベースをスケールアップまたはスケールダウンできます。次の例では、TestDB12 という名前の SQL Database を、現在のパフォーマンス レベルから Standard S3 レベルにスケールアップします。

	Set-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## SQL Database の削除

[Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) コマンドを使用して SQL Database を削除できます。次の例では TestDB12 という名前の SQL Database を削除します。

	Remove-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## サーバーの削除

また、[Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) コマンドを使用してサーバーを削除できます。次の例では server12 という名前のサーバーを削除します。

	Remove-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



これらの Azure SQL リソースまたは同様のリソースを再作成する場合は、次のことができます。

- これを PowerShell スクリプト ファイル (*.ps1) として保存します
- Azure クラシック ポータルの [Automation] セクションに、これを Azure Automation Runbook として保存する。 

## 次のステップ

コマンドを結合し、自動化します。たとえば、サーバー、ファイアウォール規則、およびデータベースを作成するために、< and > 文字を含む引用符内のすべてのものを置換します。


    New-AzureRMResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRMSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRMSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRMSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 関連情報

- [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_1203_2015-->