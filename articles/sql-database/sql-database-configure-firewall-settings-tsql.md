<properties
	pageTitle="方法: ファイアウォール設定を構成する | Microsoft Azure"
	description="Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
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
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# 方法: TSQL を使用してファイアウォール設定を構成する (SQL Database)


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT] Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。


## Transact-SQL を使用してサーバー レベルのファイアウォール規則を管理する

1. クラシック ポータルまたは SQL Server Management Studio を使用して、クエリ ウィンドウを起動します。
2. マスター データベースに接続していることを確認します。
3. サーバーレベルのファイアウォール規則は、クエリ ウィンドウから選択、作成、更新、または削除することができます。
4. サーバーレベルのファイアウォール規則を更新または作成するには、sp\_set\_firewall rule ストアド プロシージャを実行します。次の例では、Contoso サーバーの IP アドレス範囲を有効にします。<br/>まず、既に存在する規則を確認します。

		SELECT * FROM sys.firewall_rules ORDER BY name;

	次に、ファイアウォール規則を追加します。

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	サーバーレベルのファイアウォール規則を削除するには、sp\_delete\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 
## データベース レベルのファイアウォール規則

1. サーバーレベルのファイアウォールを IP アドレスで作成した後、クラシック ポータルまたは SQL Server Management Studio からクエリ ウィンドウを起動します。
2. データベースレベルのファイアウォール規則を作成するデータベースに接続します。

	既存のデータベースレベルのファイアウォール規則を新規作成または更新するには、sp\_set\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では ContosoFirewallRule という名前の新しいファイアウォール規則を作成します。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	既存のデータベースレベルのファイアウォール規則を削除するには、sp\_delete\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## 次のステップ

データベース作成のチュートリアルについては、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、「[プログラムで Azure SQL Database に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)」を参照してください。データベースに移動する方法の詳細については、「[Azure SQL Database におけるデータベース、ログインの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。

<!---HONumber=AcomDC_0211_2016-->