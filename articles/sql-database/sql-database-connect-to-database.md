<properties
	pageTitle="SSMS を使用して Azure SQL Database に接続する方法" 
	description="SSMS を使用して Azure SQL Database に接続する方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# SQL Server Management Studio を使用した SQL Database への接続方法

これらは、SQL Server Management Studio (SSMS) を使用して、Microsoft Azure SQL Database に接続する手順です。

## 前提条件
* Azure SQL Database のプロビジョニングと実行。新しい SQL Database を作成するには、「[Microsoft Azure SQL Database の概要](sql-database-get-started.md)」をご覧ください。
* SQL Database の管理者の名前とパスワードを入力します。
* SQL Server Management Studio 2014。ツールを入手するには、「[SQL Express のダウンロード](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx)」をご覧ください。
* データベースのファイアウォール設定を構成します。「[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## SQL Database のインスタンスに接続するには
1. [Azure 管理ポータル](https://portal.azure.com)にログインします。
2. **[参照]** ボタンをクリックして、**[SQL Database]** をクリックします (b)。

	![[参照] と [SQL Database] をクリックします][1]
3. **[SQL Database]** を選択した状態で (a)、接続するサーバー上のデータベースの名前をクリックします (b)。

	![データベースの名前をクリックします][2]
4. 名前を選択した状態で (a)、[プロパティ] をクリックします (b)。サーバーの名前 (c) と、管理者の名前 をコピーします (d)。管理者名とパスワードは、SQL Database の作成時に作成されます。次の手順に進むためのパスワードが必要です。

	![[SQL Server]、[設定]、[プロパティ] をクリックします][3]
5. SQL Server Management Studio 2014 を開きます。
6. [サーバーに接続] ダイアログで、サーバーの名前を **[サーバー名]** ボックス (a) に貼り付けます。認証を **[SQL Server 認証]** に設定します。サーバー管理者の名前を **[ログイン]** ボックス (c) に貼り付け、そのパスワード (d) を入力します。**[オプション]** (e) をクリックします。

	![SSMS のログイン ダイアログ ボックス][4]
7. [接続プロパティ] タブで、**[データベースへの接続]** ボックスを **[マスター]** (あるいは接続するその他のデータベース) に設定します。**[接続]** をクリックします。

	![[マスター] に設定して [接続] をクリックします。][5]

## 接続の問題のトラブルシューティング

問題が発生した場合は、「[Azure SQL Database への接続の問題のトラブルシューティング](https://support.microsoft.com/kb/2980233/)」をご覧ください。考えられる問題と回答の一覧については、「[Microsoft Azure SQL Database 接続のトラブルシューティング](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1)」をご覧ください。


## 次のステップ
Transact-SQL ステートメントを使用してデータベースを作成、管理することができます。「[CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)」と「[SQL Server Management Studio を使用した Azure SQL Database の管理](sql-database-manage-azure-ssms.md)」をご覧ください。イベントを Azure ストレージに記録することもできます。「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」をご覧ください。

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->