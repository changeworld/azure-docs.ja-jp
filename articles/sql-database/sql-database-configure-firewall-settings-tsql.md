<properties
	pageTitle="方法: SQL Database ファイアウォールの構成 | Microsoft Azure"
	description="Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/26/2016"
	ms.author="rickbyh"/>


# 方法: TSQL を使用して Azure SQL Database ファイアウォールを構成する


> [AZURE.SELECTOR]
- [概要](sql-database-firewall-configure.md)
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT] Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。


## Transact-SQL を使用してサーバー レベルのファイアウォール規則を管理する

サーバー レベルのプリンシパル ログインまたは Azure Active Directory 管理者のみが、Transact-SQL を使用して、サーバー レベルのファイアウォール ルールを作成できます。

1. クエリ ウィンドウを起動し、仮想マスター データベースに接続するには、SQL Server Management Studio を使用します。
2. サーバーレベルのファイアウォール規則は、クエリ ウィンドウから選択、作成、更新、または削除することができます。
3. サーバーレベルのファイアウォール規則を更新または作成するには、sp\_set\_firewall rule ストアド プロシージャを実行します。次の例では、Contoso サーバーの IP アドレス範囲を有効にします。<br/>まず、既に存在する規則を確認します。

		SELECT * FROM sys.firewall_rules ORDER BY name;

	次に、ファイアウォール規則を追加します。

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	サーバーレベルのファイアウォール規則を削除するには、sp\_delete\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 このストアド プロシージャの詳細については、[sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) に関するページと [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) に関するページを参照してください。

## データベース レベルのファイアウォール規則

**CONTROL** 権限が付与されているデータベース ユーザー (データベース所有者など) のみが、データベース レベルのファイアウォール規則を作成できます。

1. サーバーレベルのファイアウォールを IP アドレスで作成した後、クラシック ポータルまたは SQL Server Management Studio からクエリ ウィンドウを起動します。
2. データベースレベルのファイアウォール規則を作成するデータベースに接続します。

	既存のデータベースレベルのファイアウォール規則を新規作成または更新するには、sp\_set\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では ContosoFirewallRule という名前の新しいファイアウォール規則を作成します。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	既存のデータベースレベルのファイアウォール規則を削除するには、sp\_delete\_database\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

このストアド プロシージャの詳細については、[sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) に関するページと [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) に関するページを参照してください。

## 次のステップ

データベース作成のチュートリアルについては、「[SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、[クライアントから SQL Database にすばやく接続するためのコード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関するページを参照してください。データベースに移動する方法については、[データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関するページをご覧ください。

<!---HONumber=AcomDC_0615_2016-->