<properties
	pageTitle="T-SQL を使用した、Azure SQL Database のサーバー レベルおよびデータベース レベルのファイアウォール規則 | Microsoft Azure"
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
	ms.date="08/30/2016"
	ms.author="rickbyh"/>


# TSQL を使用して Azure SQL Database ファイアウォールを構成する


> [AZURE.SELECTOR]
- [概要](sql-database-firewall-configure.md)
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT] Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。


## サーバーレベルのファイアウォール規則

サーバー レベルのプリンシパル ログインまたは Azure Active Directory 管理者のみが、Transact-SQL を使用して、サーバー レベルのファイアウォール ルールを作成できます。

1. クエリ ウィンドウを起動し、仮想マスター データベースに接続するには、SQL Server Management Studio を使用します。
2. サーバーレベルのファイアウォール規則は、クエリ ウィンドウから選択、作成、更新、または削除することができます。
3. サーバーレベルのファイアウォール規則を作成または更新するには、`sp_set_firewall_rule` ストアド プロシージャを実行します。次の例では、Contoso サーバーの IP アドレス範囲を有効にします。<br/>まず、既に存在する規則を確認します。

		SELECT * FROM sys.firewall_rules ORDER BY name;

	次に、ファイアウォール規則を追加します。

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	サーバーレベルのファイアウォール規則を削除するには、sp\_delete\_firewall\_rule ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 このストアド プロシージャの詳細については、[sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) に関するページと [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) に関するページを参照してください。

## データベース レベルのファイアウォール規則

データベース レベルのファイアウォール規則を作成できるのは、**CONTROL** 権限が付与されているデータベース ユーザー (データベース所有者など) のみです。

1. IP アドレスに対してサーバーレベルのファイアウォールを作成した後、クラシック ポータルまたは SQL Server Management Studio からクエリ ウィンドウを起動します。
2. データベースレベルのファイアウォール規則を作成するデータベースに接続します。

	データベースレベルのファイアウォール規則を新規作成するか、既存のものを更新するには、`sp_set_database_firewall_rule` ストアド プロシージャを実行します。次の例では ContosoFirewallRule という名前の新しいファイアウォール規則を作成します。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	既存のデータベースレベルのファイアウォール規則を削除するには、`sp_delete_database_firewall_rule` ストアド プロシージャを実行します。次の例では、ContosoFirewallRule という名前の規則を削除します。' EXEC sp\_delete\_database\_firewall\_rule @name = N'ContosoFirewallRule'

このストアド プロシージャの詳細については、[sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) に関するページと [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) に関するページを参照してください。

## 次のステップ

他の方法でサーバー レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。

- [Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings.md)
- [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
- [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」をご覧ください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、[SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。データベースに移動する方法については、[データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。


## その他のリソース

- [データベースの保護](sql-database-security.md)
- [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0831_2016-->