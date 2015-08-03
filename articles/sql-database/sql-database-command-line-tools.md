<properties 
	pageTitle="PowerShell を使用した Azure SQL Database リソースの管理" 
	description="PowerShell を使用した Azure SQL Database の管理。" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="vinsonyu"/>

# PowerShell を使用した Azure SQL Database リソースの管理


このトピックでは、Azure リソース マネージャー コマンドレットを使用した、多数の Azure SQL Database タスクを実行する PowerShell コマンドを紹介します。


## 前提条件

PowerShell コマンドを実行するには、Azure PowerShell がインストールされ実行されていること、リソース マネージャー モードに切り替えて Azure リソース マネージャーの PowerShell コマンドレットを使用できるようにしておくことが必要です。

Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。

Azure SQL Database の作成と管理に使うコマンドレットは、Azure リソース マネージャー モジュールにあります。Azure PowerShell を開始するときに、Azure モジュールのコマンドレットが既定でインポートされます。Azure リソース マネージャー モジュールに切り替えるには、**Switch-AzureMode** コマンドレットを使用します。

	PS C:\>Switch-AzureMode -Name AzureResourceManager

「Switch-AzureMode コマンドレットは廃止予定で、今後のリリースで削除される予定です。」という警告が表示される場合、 無視して次のセクションに移動してください。

詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。



## 資格情報の構成

Azure サブスクリプションに PowerShell コマンドレットを実行するには、Azure アカウントにまずアクセスできるようにする必要があります。次を実行すると資格情報を入力するサインイン画面が表示されます。Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	PS C:\>Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


## Azure サブスクリプションを選択します。

使用するサブスクリプションを選択するには、サブスクリプション ID (**-SubscriptionId**) とサブスクリプション名 (**-SubscriptionName**) が必要になります。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。

現在のサブスクリプションを設定するには、サブスクリプション情報と共に次のコマンドレットを実行します。

	PS C:\>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

上で選択したサブスクリプションに対し、次のコマンドが実行されます。

## リソース グループの作成

サーバーを含むリソース グループを作成します。任意の有効な場所を使用するには、次のコマンドを編集します。

有効な Azure SQL Database サーバーの場所一覧については、次のコマンドレットを実行します。

		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

既にリソース グループがある場合、サーバーの作成に進みます。次のコマンドを実行して新しいリソース グループを作ることもできます。

	PS C:\>New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## サーバーの作成 

新しい V12 サーバーを作成するには、[New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx) コマンドを使用します。server12 をご利用のサーバー名に置き換えます。サーバー名がすでに使われている場合はエラーが発生する可能性があるため、Azure SQL Server で一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。サーバーが正常に作成されると、サーバーの詳細と PowerShell のプロンプトが表示されます。コマンドを編集すると、任意の有効な場所を使用できます。

	PS C:\>New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

## サーバー ファイアウォール規則の作成

サーバーへアクセスするために、ファイアウォール規則を作成するには、[New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx) コマンドを使用します。ご利用のクライアントで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

サーバーで別の Azure サービスへのアクセス許可が必要な場合は、**- AllowAllAzureIPs** スイッチを追加して特別なファイアウォール規則を追加し、サーバーへのすべての Azure トラフィック アクセスを許可します。

	PS C:\>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。

## SQL Database の作成

データベースを作成するには、[New-azuresqldatabase](https://msdn.microsoft.com/library/mt125915.aspx) コマンドを使用します。データベースを作成するには、サーバーが必要です。次の例では TestDB12 という名前の SQL Database を作成します。このデータベースは、Standard S1 データベースとして作成されます。

	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## SQL Database のパフォーマンス レベルを変更します

[Set-azuresqldatabase](https://msdn.microsoft.com/library/mt125814.aspx) コマンドを使用すると、データベースをスケールアップまたはダウンできます。次の例では、TestDB12 という名前の SQL Database を、現在のパフォーマンス レベルから標準的な S3 レベルにスケールアップします。

	PS C:\>Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## SQL Database の削除

SQL Database は、[Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx) コマンドを使用して削除できます。次の例では TestDB12 という名前の SQL Database を削除します。

	PS C:\>Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## サーバーの削除

サーバーは、[Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx) コマンドを使用して削除することもできます。次の例では server12 という名前のサーバーを削除します。

	PS C:\>Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



これらの Azure SQL リソースまたは同様のリソースを再作成する場合は、次のことができます。

- これを PowerShell スクリプト ファイル (*.ps1) として保存します
- Azure 管理ポータルの [オートメーション] セクションに、これを Azure の Automation Runbook として保存する。 

## 次のステップ

コマンドを結合し、自動化します。たとえば、サーバー、ファイアウォール規則、およびデータベースを作成するために、< and > 文字を含む引用符内のすべてのものを置換します。


    PS C:\>New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    PS C:\>New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    PS C:\>New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    PS C:\>New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 関連情報

- [Azure SQL Database リソース マネージャー コマンドレット](https://msdn.microsoft.com/library/mt163521.aspx)
- [Azure SQL Database サービス管理コマンドレット](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=July15_HO4-->