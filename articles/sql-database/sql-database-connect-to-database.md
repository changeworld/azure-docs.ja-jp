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
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# SQL Server Management Studio での接続

これらは、SQL Server Management Studio (SSMS) をインストールして、SSMS を使用し、SQL Database に接続して、クエリを実行する手順です。

## 前提条件
* 「[最初の Azure SQL データベースを作成する](sql-database-get-started.md)」で説明されている SQL Database AdventureWorks のサンプル データベース。

## SQL Server Management Studio (SSMS) のインストールと起動
1. [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299) のダウンロード ページに移動し、**[ダウンロード]** をクリックして、MgmtStudio ダウンロードの 32 ビット バージョン (x86) または 64 ビット バージョン (x64) のいずれかを選択します。

	![MgtmtStudio32BIT または MgmtStudio64BIT][1]
2.	既定の設定を使用して SSMS をインストールするときは、プロンプトの指示に従います。
3.	ダウンロードしたら、PC 上の SQL Server 2014 Management Studio を検索し、SSMS を起動します。


## SQL Database に接続する
1. SSMS を開きます。
2. **[サーバーに接続]** ウィンドウの **[サーバー名]** ボックスで、*&lt;servername>*.**database.windows.net** の形式でサーバーの名前を入力します。
3. **[認証]** ボックスの一覧で、**[SQL Server 認証]** を選択します。
4. SQL Database サーバーを作成したときに指定した**ログイン情報**と**パスワード**を入力します。

	![[サーバーに接続] ダイアログ][2]
5. **[オプション]** ボタンをクリックします。
6. **[データベースへの接続]** ボックスで、「**AdventureWorks**」を入力し、**[接続]** をクリックします。

	![データベースへの接続][3]

### 接続が失敗した場合
作成した論理サーバーのファイアウォールによって、ローカル コンピューターからの接続が許可されていることを確認します。「[方法: ファイアウォール設定を構成する (Azure SQL データベース)](https://msdn.microsoft.com/library/azure/jj553530.aspx)」を参照してください。

## サンプル クエリの実行

1. **オブジェクト エクスプローラー**で、**[AdventureWorks]** データベースに移動します。
2. データベースを右クリックし、**[新しいクエリ]** を選択してクリックします。

	![新しいクエリ][4]
3. 新しく開かれたクエリ ウィンドウに、次のコードをコピーして貼り付けます。

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. 次に、**[実行]** ボタンをクリックします。成功すると、次のような画面が表示されます: ![成功][5]


## 次のステップ
Transact-SQL ステートメントを使用してデータベースを作成、管理することができます。「[CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)」と「[SQL Server Management Studio を使用した Azure SQL Database の管理](sql-database-manage-azure-ssms.md)」をご覧ください。イベントを Azure ストレージに記録することもできます。「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」をご覧ください。

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=July15_HO4-->