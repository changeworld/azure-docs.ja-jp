<properties
	pageTitle="SQL Database のサーバー レベルのファイアウォール規則の構成 | Microsoft Azure"
	description="Azure SQL サーバーにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
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
	ms.author="rickbyh;carlrab"/>


# Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する


> [AZURE.SELECTOR]
- [概要](sql-database-firewall-configure.md)
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)

Azure SQL Server では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Server 論理サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。このトピックでは、サーバーレベルのファイアウォール規則について説明します。

> [AZURE.IMPORTANT] Azure のアプリケーションから Azure SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則の働きを理解するには、[Azure SQL サーバーのファイアウォールの構成方法の概要](sql-database-firewall-configure.md)に関するページを参照してください。Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。

**推奨:** アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバーレベルのファイアウォール規則を使用してください。ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベースレベルのファイアウォール規則を使用することをお勧めします。

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する

サーバーレベルのファイアウォール規則を管理する手順を繰り返します。

- 現在のコンピューターを追加するには、[クライアント IP の追加] をクリックします。
- さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。
- 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
- 既存の規則を削除するには、行の最後に X が表示されるまで、規則上にポインターを置きます。[X] をクリックして、規則を削除します。

**[保存]** をクリックして変更を保存します。

## 次のステップ

Transact-SQL を使用して、サーバー レベルとデータベース レベルのファイアウォール規則を作成する方法については、「[T-SQL を使用して Azure SQL Database のサーバー レベルとデータベース レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-tsql.md)」をご覧ください。

他の方法でサーバー レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。

- [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
- [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、[SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。データベースに移動する方法については、[データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。


## その他のリソース

- [データベースの保護](sql-database-security.md)
- [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0831_2016-->