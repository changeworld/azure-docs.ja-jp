<properties
	pageTitle="Windows 上で Python を使用して SQL Database に接続する"
	description="Azure SQL Database への接続に使用できる Python コード サンプルについて説明します。このサンプルは、pymssql ドライバーを使用します。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="meetb"/>


# Windows 上で Python を使用して SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは Python で記述されたコード サンプルについて説明します。サンプルは、Windows コンピューター上で実行されます。サンプルは、**pymssql** ドライバーを使用して、Azure SQL Database に接続されます。


## 前提条件


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### 必要なモジュールのインストール


[pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql) をインストールします。

正しい whl ファイルを選択していることをご確認ください。

たとえば、64 ビット コンピューター上で Python 2.7 を使用している場合は、pymssql‑2.1.1‑cp27‑none‑win\_amd64.whl を選択します。.whl ファイルをダウンロードしたら、C:/Python27 フォルダーに配置します。

これでコマンドライン .cd から pip を使用して、pymssql ドライバーを C:/Python27 にインストールし、次を実行します

	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

pip の使用を有効にする手順については、[ここ](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)をご覧ください


### SQL Database

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。

## 手順 1. 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 手順 2. 接続する


[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 関数は、SQL Database に接続するために使用します。

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## 手順 3. クエリを実行する

[Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 関数は、SQL Database に対するクエリから結果セットを取得するために使用できます。この関数は基本的に任意のクエリを受け取り、[cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) を使用して反復処理できる結果セットを返します。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 手順 4. 行を挿入する

この例では、[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) ステートメントを安全に実行し、[SQL インジェクション](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) の脆弱性からアプリケーションを保護するパラメーターを渡し、自動生成された[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値を取得する方法について説明しています。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## 手順 5. トランザクションをロールバックする


このコード例は、以下のトランザクションの使用について示します。


- トランザクションの開始

- データの挿入

- トランザクションをロールバックして、挿入を元に戻す


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

## 次のステップ

詳細については、[Python デベロッパー センター](/develop/python/)を参照してください。

<!---HONumber=AcomDC_1210_2015-->