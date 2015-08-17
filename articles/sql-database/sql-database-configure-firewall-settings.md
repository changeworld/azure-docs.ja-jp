<properties
	pageTitle="ファイアウォール設定の構成方法 (Azure SQL Database)"
	description="Azure SQL Database のファイアウォールの構成"
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/22/2015"
	ms.author="rickbyh"/>


# ファイアウォール設定の構成方法 (Azure SQL Database)

 Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

**重要** Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」を参照してください。


## サーバーレベルのファイアウォール規則

サーバーレベルのファイアウォール規則は、Microsoft Azure の管理ポータル、Transact-SQL、Azure PowerShell、または REST API を使用して管理できます。

### 新しい Azure ポータルを介したサーバーレベルのファイアウォール規則の管理
1. https://portal.azure.com にある Azure ポータルにアクセスし、Azure の管理者または共同作成者のアカウントでサインインします。
2. 左のバナーの [すべてを参照] をクリックして、スクロールダウンして [SQL Server] をクリックします。
3. 表示されている SQL サーバーの一覧から、ファイアウォール規則を構成するサーバーをクリックします。

	![ファイアウォール][1]

4. サーバーのブレードで、ブレード上部の [設定] をクリックして、[ファイアウォール] をクリックして、サーバーの [ファイアウォール設定] ブレードをクリックしてください。
5. ファイアウォールの規則を追加または変更します。

	* 現在のコンピューターの IP アドレスを追加するには、ブレードの上部の **[クライアント IP の追加]**をクリックします。
	* さらに IP アドレスを追加するには、**[規則名]**、**[開始 IP]** アドレス、および** [終了 IP]** アドレスを入力します。
	* 既存の規則を変更するには、規則内の任意のフィールドをクリックして変更します。
	* 既存の規則を削除するには、規則をクリックして、行の最後の省略記号 (...) をクリックして、**[削除]** をクリックします。
6. [ファイアウォール設定] ブレード上部の [保存] をクリックし、変更を保存します。![ファイアウォール ブレード][2] 

## 管理ポータルを使用したサーバーレベルのファイアウォール規則の管理 

1. 管理ポータルで **[SQL Database]** をクリックします。すべてのデータベースと、対応するサーバーが表示されます。
1. ページの上部にある **[サーバー]** をクリックします。
2. ファイアウォール規則を管理するサーバーの横の矢印をクリックします。
3. ページの上部にある **[構成]** をクリックします。

	*  現在のコンピューターを追加するには、[使用できる IP アドレスに追加します] をクリックします。
	*  さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。
	*  既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
	*  既存の規則を削除するには、行の最後に X が表示されるまで、規則上にポインターを置きます。[X] をクリックして、規則を削除します。
8. ページ下部の **[保存]** をクリックし、変更内容を保存します。

## Transact-SQL を使用したサーバーレベルのファイアウォール規則の管理
1. 管理ポータルまたは SQL Server Management Studio を使用して、クエリ ウィンドウを起動します。
2. マスター データベースに接続していることを確認します。
3. サーバーレベルのファイアウォール規則は、クエリ ウィンドウから作成、更新、または削除することができます。
4. サーバーレベルのファイアウォール規則を更新または作成するには、sp\_set\_firewall rule ストアド プロシージャを実行します。次の例では、サーバー Contoso の IP アドレス範囲を有効にします。

		EXEC sp_set_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	サーバーレベルのファイアウォール規則を削除するには、sp\_delete\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXEC sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Azure PowerShell を使用したサーバーレベルのファイアウォール規則の管理
1. Azure PowerShell を起動します。
2. Azure PowerShell を使用すると、サーバーレベルのファイアウォール規則を作成、更新、および削除できます。 

	新しいサーバーレベルのファイアウォール規則を作成するには、New-AzureSqlDatabaseServerFirewallRule コマンドレットを実行します。次の例では、サーバー Contoso の IP アドレス範囲を有効にします。
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	既存のサーバーレベルのファイアウォール規則を変更するには、Set-azuresqldatabaseserverfirewallrule コマンドレットを実行します。次の例では、ContosoFirewallRule という名前の規則で許容される IP アドレスの範囲を変更します。
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	既存のサーバーレベルのファイアウォール規則を削除するには、Remove-AzureSqlDatabaseServerFirewallRule コマンドレットを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## REST API を介したサーバーレベルのファイアウォール規則の管理
1. REST API を介してファイアウォール規則を管理する場合、認証される必要があります。詳細については、「サービス管理要求の認証」を参照してください。
2. サーバーレベルの規則は、REST API を使用して、作成、更新、または削除できます。

	サーバーレベルのファイアウォール規則を作成または更新するには、次を使用して、POST メソッドを実行します。
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	要求本文

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	既存のサーバーレベルのファイアウォール規則を削除するには、次を使用して、DELETE メソッドを実行します。
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## データベースレベルのファイアウォール規則

1. サーバーレベルのファイアウォールを IP アドレスで作成した後、管理ポータルまたは SQL Server Management Studio からクエリ ウィンドウを起動します。
2. データベースレベルのファイアウォール規則を作成するデータベースに接続します。

	既存のデータベースレベルのファイアウォール規則を新規作成または更新するには、sp\_set\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では ContosoFirewallRule という名前の新しいファイアウォール規則を作成します。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	既存のデータベースレベルのファイアウォール規則を削除するには、sp\_delete\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## サービス管理 REST API を使用したファイアウォール規則の管理

* [ファイアウォール規則の作成](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [ファイアウォール規則の削除](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [ファイアウォール規則の取得](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [ファイアウォール規則の一覧表示](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## PowerShell を使用したファイアウォール規則の管理

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## 次のステップ

データベース作成のチュートリアルについては、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、「[プログラムで Azure SQL Database に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)」を参照してください。データベースに移動する方法の詳細については、「[Azure SQL Database におけるデータベース、ログインの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=August15_HO6-->