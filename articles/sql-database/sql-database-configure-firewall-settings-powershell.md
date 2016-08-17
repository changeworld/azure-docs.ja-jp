<properties
	pageTitle="PowerShell を使用して Azure SQL Database のサーバーレベルのファイアウォール規則を構成する | Microsoft Azure"
	description="Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="sstein"/>


# PowerShell を使用して Azure SQL Database のサーバーレベルのファイアウォール規則を構成する


> [AZURE.SELECTOR]
- [概要](sql-database-firewall-configure.md)
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT] Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。詳細については、「[Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)](sql-database-develop-direct-route-ports-adonet-v12.md)」の「SQL Database V12: 外部と内部」セクションを参照してください。


[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## サーバー ファイアウォール規則の作成

Azure PowerShell を使用すると、サーバーレベルのファイアウォール規則を作成、更新、および削除できます。

新しいサーバーレベルのファイアウォール規則を作成するには、New-AzureRmSqlServerFirewallRule コマンドレットを実行します。次の例では、サーバー Contoso の IP アドレス範囲を有効にします。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'		

既存のサーバーレベルのファイアウォール規則を変更するには、Set-azuresqldatabaseserverfirewallrule コマンドレットを実行します。次の例では、ContosoFirewallRule という名前の規則で許容される IP アドレスの範囲を変更します。

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

既存のサーバーレベルのファイアウォール規則を削除するには、Remove-AzureSqlDatabaseServerFirewallRule コマンドレットを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## PowerShell を使用したファイアウォール規則の管理

ファイアウォール規則の管理に PowerShell を使用することもできます。詳細については、次のトピックを参照してください。

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)


## 次のステップ

Transact-SQL を使用して、サーバーレベルおよびデータベースレベルのファイアウォール規則を作成する方法の詳細については、「[TSQL を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-tsql.md)」をご覧ください。

他の方法でサーバーレベルのファイアウォール規則を作成する方法の詳細については、次の記事をご覧ください。

- [Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings.md)
- [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」をご覧ください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、[SQL Database のクライアント クイックスタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関するページをご覧ください。データベースに移動する方法については、[データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関するページをご覧ください。


## その他のリソース

- [データベースの保護](sql-database-security.md)
- [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0803_2016-->