<properties
	pageTitle="Windows から SQL DB に接続する PHP | Microsoft Azure"
	description="Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# Windows 上で PHP を使用して SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Windows 上で実行される PHP で記述されたクライアント アプリケーションから Azure SQL Database に接続する方法について説明します。


## 前提条件


このトピックで指定された PHP のコード サンプルを実行するには、クライアント コンピューターに、以下のソフトウェア アイテムがインストールされていることが必要です。


- [Microsoft Drivers for PHP for Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE には、最新のビットが含まれています)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Microsoft ODBC Driver](https://www.microsoft.com/ja-jp/download/details.aspx?id=36434)
- IIS Express
- [PHP 5.6 for IIS Express](http://www.microsoft.com/web/downloads/platform.aspx): プラットフォーム インストーラーを使ってダウンロードします。Internet Explorer を使ってプラットフォーム インストーラーをダウンロードしてください。

[チーム ブログ](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx)と[ビデオ](https://www.youtube.com/watch?v=0oCjiRK_tUk)をチェックして、上記の要件をインストールおよびセットアップする方法を確認してください。


## データベースを作成し、接続文字列を取得します。


「[トピックの開始」](sql-database-get-started.md)」ページで、サンプル データベースの作成方法と接続文字列を取得する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## SQL Database のデータベースに接続する


**OpenConnection** 関数は、後に続くすべての関数の上部近くで呼び出されます。


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## クエリを実行し、結果セットを取得します

[sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) 関数は、SQL Database に対するクエリから結果セットを取得するために使用できます。この関数は基本的に任意のクエリと接続オブジェクトを受け取り、[sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) を使用して反復処理できる結果セットを返します。

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## 行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する


SQL Database の [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトは、[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値の自動生成に使用できます。


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";	
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## トランザクション


このコード例は、以下のトランザクションの使用について示します。

- トランザクションの開始

- データ行の挿入、別のデータ行の更新

- 挿入と更新に成功した場合はトランザクションをコミット。そのどちらか一方に失敗した場合はトランザクションをロールバック


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 参考資料


PHP のインストールと使用に関する詳細については、「[Accessing SQL Server Databases with PHP (PHP を使用して SQL Server のデータベースにアクセスする)](http://technet.microsoft.com/library/cc793139.aspx)」をご覧ください。

 

<!---HONumber=58_postMigration-->