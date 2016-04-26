<properties
	pageTitle="SQL Database への接続 - SQL Server Management Studio | Microsoft Azure"
	description="SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。次に、TRANSACT-SQL (T-SQL) を使用して、サンプル クエリを実行します。"
	metaCanonical=""
	keywords="SQL データベースへの接続、SQL Server Management Studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="sstein;carlrab" />

# SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

この記事では、SQL Server Management Studio (SSMS) の最新バージョンを使用して Azure SQL Database に接続し、TRANSACT-SQL (T-SQL) ステートメントを使用して簡単なクエリを実行する方法を示します。

[AZURE.INCLUDE [サインイン](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS のインストール](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS 接続](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

ファイアウォール規則の詳細については、「[方法: ファイアウォール設定を構成する (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## サンプル クエリの実行

論理サーバーに接続すると、データベースに接続し、サンプル クエリを実行できます。

1. **オブジェクト エクスプローラー**で、サーバー上のアクセス許可を持っているデータベース (**AdventureWorks** サンプル データベースなど) に移動します。
2. データベースを右クリックし、**[新しいクエリ]** を選択します。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. クエリ ウィンドウに、次のコードをコピーして貼り付けます。

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **[実行]** ボタンをクリックします。次のスクリーン ショットに、成功したクエリを示します。

	![成功。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## 次のステップ

SQL Server で可能な方法とほぼ同じように、T-SQL ステートメントを使用して Azure にデータベースを作成して管理することもできます。SQL Server で T-SQL を使用するのに慣れている場合は、「[Azure SQL Database TRANSACT-SQL 情報](sql-database-transact-sql-information.md)」で相違点の概要を参照してください。

T-SQL を初めて使用する場合は、「[チュートリアル: TRANSACT-SQL ステートメントの作成](https://msdn.microsoft.com/library/ms365303.aspx)」と「[TRANSACT-SQL リファレンス (データベース エンジン)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。

データベース ユーザーおよびデータベース ユーザー管理者の作成の概要については、「[Get Started with Azure SQL Database security (Azure SQL Database セキュリティの概要)](sql-database-get-started-security.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->