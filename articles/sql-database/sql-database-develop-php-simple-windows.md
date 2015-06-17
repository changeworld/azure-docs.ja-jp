<properties
	pageTitle="Windows 上で PHP を使用して SQL Database に接続する"
	description="Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="genemi"/>


# Windows 上で PHP を使用して SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Windows 上で実行される PHP で記述されたクライアント アプリケーションから Azure SQL Database に接続する方法について説明します。


## 前提条件


このトピックで指定された PHP のコード サンプルを実行するには、クライアント コンピューターに、以下のソフトウェア アイテムがインストールされていることが必要です。


- [Microsoft Drivers for PHP for Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE には、最新のビットが含まれています)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用して実行する特定のインストール。詳細については、次のセクションをご覧ください。


### Web Platform Installerによるインストール


1. [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) をダウンロードして、実行し、必要なモジュールを選択します。
2. [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用して次のコンポーネントをインストールします。
 - IIS か IIS Express。<br/>たとえば、IIS Express を Web で検索すると、[IIS Express 8.0 のダウンロード](http://www.microsoft.com/download/details.aspx?id=34679)が見つかります。
 - Windows 用 PHP、5.5 以降のバージョン、32 ビット版。
3. [Dynamic Extensions] セクションの下にエントリを追加して以下のように PHP.INI ファイル を編集します。<br/>**extension=php_sqlsrv_55_nts.dll**


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


## テーブルに値を挿入します。


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT INTO [SalesLT].[Customer]
					   ([NameStyle],[FirstName],[MiddleName],[LastName],
					   [PasswordHash],[PasswordSalt],[rowguid],[ModifiedDate])
					   VALUES (?,?,?,?,?,?,?,?)";

			$params = array("0", "Luiz", "F", "Santos",
				"L/Rlwxzp4w7RWmEgXX+/A7cXaePEPcp+KwQhl2fJL7w=",
				"1KjXYs4=", "88949BFE-0BB4-4148-AFC2-DD8C8DAE4CD6",
				date("Y-m-d"));

			$insertReview = sqlsrv_prepare($conn, $tsql, $params);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($insertReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($insertReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## テーブルから値を削除します。


	function DeleteData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "DELETE FROM [SalesLT].[Customer] WHERE FirstName=? AND LastName=?";
			$params = array($_REQUEST['FirstName'], $_REQUEST['LastName']);

			$deleteReview = sqlsrv_prepare($conn, $tsql, $params);
			if($deleteReview == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			if(sqlsrv_execute($deleteReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($deleteReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## テーブルから値を選択します。


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


## トランザクション


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			/* Initialize parameter values. */
			$orderId = 43659;
			$qty = 5;
			$productId = 709;
			$offerId = 1;
			$price = 5.70;

			/* Set up and execute the first query. */
			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail
			   (SalesOrderID,OrderQty,ProductID,SpecialOfferID,UnitPrice)
			   VALUES (?, ?, ?, ?, ?)";
			$params1 = array($orderId, $qty, $productId, $offerId, $price);
			$stmt1 = sqlsrv_query($conn, $tsql1, $params1);

			/* Set up and execute the second query. */
			$tsql2 = "UPDATE Production.ProductInventory SET Quantity = (Quantity - ?)
					  WHERE ProductID = ?";
			$params2 = array($qty, $productId);
			$stmt2 = sqlsrv_query( $conn, $tsql2, $params2 );

			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
					sqlsrv_commit($conn);
					echo "Transaction was committed.\n";
			}
			else
			{
					sqlsrv_rollback($conn);
					echo "Transaction was rolled back.\n";
			}

			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## ストアド プロシージャ


	function ExecuteSProc()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "EXEC [dbo].[uspLogError]";

			$execReview = sqlsrv_prepare($conn, $tsql);
			if($execReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($execReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($execReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 参考資料


PHP のインストールと使用に関する詳細については、「[Accessing SQL Server Databases with PHP (PHP を使用して SQL Server のデータベースにアクセスする)](http://technet.microsoft.com/library/cc793139.aspx)」をご覧ください。

<!---HONumber=58--> 