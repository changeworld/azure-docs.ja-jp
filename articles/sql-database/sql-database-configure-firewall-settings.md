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
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# 方法: Azure ポータルを使用してファイアウォール設定を構成する (SQL Database)


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT]Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。ファイアウォール規則と Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。


### 新しい Azure ポータルでサーバー レベルのファイアウォール規則を管理する


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## 管理ポータルでサーバー レベルのファイアウォール規則を管理する 

1. 管理ポータルで **[SQL Database]** をクリックします。すべてのデータベースと、対応するサーバーが表示されます。
2. ページの上部にある **[サーバー]** をクリックします。
3. ファイアウォール規則を管理するサーバーの横の矢印をクリックします。
4. ページの上部にある **[構成]** をクリックします。

	*  現在のコンピューターを追加するには、[使用できる IP アドレスに追加します] をクリックします。
	*  さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。
	*  既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
	*  既存の規則を削除するには、行の最後に X が表示されるまで、規則上にポインターを置きます。[X] をクリックして、規則を削除します。
5. ページ下部の **[保存]** をクリックし、変更内容を保存します。


## 次のステップ

データベース作成のチュートリアルについては、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、「[プログラムで Azure SQL Database に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)」を参照してください。データベースに移動する方法の詳細については、「[Azure SQL Database におけるデータベース、ログインの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->