<properties 
	pageTitle="Windows 上で Node.js を使用して、SQL Database に接続する" 
	description="Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。サンプルは、Windows クライアント コンピューター上で実行されます。"
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/19/2015"
	ms.author="meetb"/>


# Windows 上で Node.js を使用して、SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。Node.js プログラムは、Windows クライアント コンピューター上で実行されます。接続の管理には、msnodesql ドライバーが使用されます。


## 必要条件


次のソフトウェア アイテムが、クライアント開発コンピューターに存在する必要があります。


-  [Node.js](https://nodejs.org/en/download/) - Windows インストーラーをクリックし、適切な msi インストーラーをダウンロードします。ダウンロードしたら、msi を実行し、Node.js をインストールします。 


### 必要なモジュールのインストール

**ノード** でコンピューターを構成したら、cmd.exe を開き、Node.js プロジェクトの作成を予定しているディレクトリに移動して、次のコマンドを入力します。


	npm init
	npm install tedious


**npm init** はノード プロジェクトを作成します。プロジェクトの作成中に既定値を保持するには、プロジェクトが作成されるまで Enter キーを押します。プロジェクト ディレクトリに **package.json** が表示されます。


### AdventureWorks データベースの作成


このトピックのコード サンプルには、**AdventureWorks** テスト データベースが必要です。まだお持ちでない場合は、「[SQL Database の使用](sql-database-get-started.md)」をご覧ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## SQL Database に接続する

[新しい Connection ](http://pekim.github.io/tedious/api-connection.html)関数は、SQL Database に接続するために使用します。

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## SQL SELECT の実行


[新しい Request()](http://pekim.github.io/tedious/api-request.html) 関数 を使用して、すべての SQL ステートメントが実行されます。ステートメントが SELECT ステートメントなどの行を返す場合は、[request.on()](http://pekim.github.io/tedious/api-request.html) 関数を使用してそれらを取得することができます。行が存在しない場合は、[request.on()](http://pekim.github.io/tedious/api-request.html) 関数は空のリストを返します。


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## 行を挿入し、パラメーターを適用し、生成されたプライマリ キーを取得する


SQL Database では、[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトを使用して、[プライマリ キーの値](https://msdn.microsoft.com/library/ms179610.aspx)を自動生成できます。この例では、INSERT ステートメントを実行し、SQL インジェクションから保護されているパラメーターを安全に渡し、自動生成されたプライマリ キー値を取得する方法について説明しています。


このセクションのコード サンプルは、SQL の INSERT ステートメントにパラメーターを適用します。生成されるプライマリ キーの値は、プログラムによって取得されます。


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

 

<!---HONumber=AcomDC_1125_2015-->