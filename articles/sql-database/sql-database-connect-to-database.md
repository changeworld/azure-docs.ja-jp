<properties
	urlDisplayName="How to connect to an Azure SQL database using SQL Server Management Studio (SSMS)"
	pageTitle="SSMS を使用して Azure SQL Database に接続する方法 | Microsoft Azure"
	metaKeywords=""
	description="SSMS を使用して Azure SQL Database に接続する方法について説明します。"
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	title="How to connect to an Azure SQL database using SSMS"
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/14/2015"
	ms.author="sidneyh" />

# SQL Server Management Studio (SSMS) での接続

次の手順に従って、SQL Server Management Studio (SSMS) を使用して SQL Database に接続し、クエリを実行します。

## 前提条件

* SQL Server Management Studio (SSMS) - 最新版の SSMS をダウンロードする方法については、「[SQL Server Management Studio のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」を参照してください。
* 「[Microsoft Azure SQL Database の概要](sql-database-get-started.md)」で説明されている AdventureWorks のサンプル データベース。


## 完全修飾 Azure SQL サーバー名を取得します。

データベースに接続するには、接続先のデータベースを含むサーバーの完全名が必要です (****servername**.database.windows.net*)。

1. [Azure プレビュー ポータル](https://portal.azure.com)にアクセスします。
2. 接続先のデータベースを探します。
3. サーバーの完全名を見つけます。

    ![完全修飾サーバー名][6]

    下の手順 3 でサーバーの完全修飾名を使用します。



## SQL Database に接続する

1. SSMS を開きます。
2. **[接続]** > **[データベース エンジン]**の順にクリックします。

    ![[接続] > [データベース エンジン]][7]

2. **[サーバーに接続]** ダイアログ ボックスの **[サーバー名]** ボックスで、*&lt;servername>*.**database.windows.net** の形式でサーバーの名前を入力します。
3. **[認証]** リストで、**[SQL Server 認証]** を選択します。
4. SQL Database サーバーを作成したときに指定した**ログイン情報**と**パスワード**を入力し、**[接続]** をクリックします。

	![[サーバーに接続] ダイアログ][2]



### 接続が失敗した場合
作成した論理サーバーのファイアウォールによって、ローカル コンピューターからの接続が許可されていることを確認します。詳細については、「[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## サンプル クエリの実行

1. **オブジェクト エクスプローラー**で、**[AdventureWorks]** データベースに移動します。
2. データベースを右クリックし、**[新しいクエリ]** を選択します。

	![新しいクエリ][4]

3. クエリ ウィンドウに、次のコードをコピーして貼り付けます。

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **[実行]** ボタンをクリックします。次のスクリーン ショットに、成功したクエリを示します。

	![成功][5]




## 次のステップ
Transact-SQL ステートメントを使用してデータベースを作成、管理することができます。詳細については、「[CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)」と「[SQL Server Management Studio を使用した Azure SQL Database の管理](sql-database-manage-azure-ssms.md)」をご覧ください。イベントを Azure ストレージに記録することもできます。詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」をご覧ください。

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=Sept15_HO3-->