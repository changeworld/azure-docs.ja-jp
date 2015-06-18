<properties 
	pageTitle="Ubuntu 上で pymssql を含む Python を使用して SQL Database に接続する" 
	description="Azure SQL Database への接続に使用できる Python コード サンプルについて説明します。このサンプルは、Ubuntu Linux クライアント コンピューター上で実行されます。"
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mebha"/>


# Ubuntu Linux 上で Python を使用して SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Azure SQL Database のデータベースに接続するために、Ubuntu Linux のクライアント コンピューター上で実行される Python のコード サンプルについて説明します。


## 必要条件


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)。


### 必要なモジュールのインストール


ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。次のコマンドを入力して、**FreeTDS** と **pymssql** をインストールします。pymssql は FreeTDS を使用して SQL Database に接続します。

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### データベースを作成し、接続文字列を取得します。


「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成し、接続文字列を取得する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## SQL Database に接続する


[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 関数は、SQL Database に接続するために使用します。

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## SQL SELECT ステートメントの実行

[Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 関数は、SQL Database に対するクエリから結果セットを取得するために使用できます。この関数は基本的に任意のクエリを受け取り、[cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) を使用して反復処理できる結果セットを返します。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する

SQL Database の [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトは、[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値の自動生成に使用できます。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## トランザクション


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

<!---HONumber=58--> 