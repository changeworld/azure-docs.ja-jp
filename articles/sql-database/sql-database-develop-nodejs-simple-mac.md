<properties
	pageTitle="Mac OS X 上で Tedious を含む Node.js を使用して SQL Database に接続する"
	description="Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。サンプルは、Tedious ドライバーを使用して接続します。"
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
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Mac OS X 上で Tedious を含む Node.js を使用して SQL Database に接続する


> [AZURE.SELECTOR]
- [Node.js](sql-database-develop-nodejs-simple-mac.md)
- [Python](sql-database-develop-python-simple-mac-osx.md)
- [Ruby](sql-database-develop-ruby-simple-mac-osx.md)


このトピックでは、Mac OS X 上で実行する Node.js のコード サンプルを示します。このサンプルは、Tedious ドライバーを使用して、Azure SQL Database に接続します。


## 前提条件


既にコンピューターにインストールされている場合を除いて、**node** をインストールします。


OSX 10.10 Yosemite に node.js をインストールするには、簡単にインストールができる、便利な事前コンパイルされたバイナリ パッケージをダウンロードします。[nodejs.org に進み](http://nodejs.org/)、インストール ボタンをクリックして最新のパッケージをダウンロードします。

**node** と **npm** の両方のインストールを行うインストール ウィザードに従い、.dmg からパッケージをインストールします (npm は、node.js 用にその他のパッケージのインストールを容易にするノード パッケージ マネージャーです)。


**ノード** と **npm** でコンピューターを構成したら、Node.js プロジェクトの作成を予定しているディレクトリに移動して、次のコマンドを入力します。


	npm init
	npm install tedious


**npm init** はノード プロジェクトを作成します。プロジェクトの作成中に既定値を保持するには、プロジェクトが作成されるまで Enter キーを押します。プロジェクト ディレクトリに **package.json** が表示されます。

### SQL Database

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。

## 手順 1. 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 手順 2. 接続する

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


## 手順 3. クエリを実行する


[新しい Request()](http://pekim.github.io/tedious/api-request.html) 関数 を使用して、すべての SQL ステートメントが実行されます。ステートメントが SELECT ステートメントなどの行を返す場合は、[request.on()](http://pekim.github.io/tedious/api-request.html) 関数を使用してそれらを取得することができます。行が存在しない場合は、[request.on()](http://pekim.github.io/tedious/api-request.html) 関数は空のリストを返します。


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
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


## 手順 4. 行を挿入する

この例では、[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) ステートメントを安全に実行し、[SQL インジェクション](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) の脆弱性からアプリケーションを保護するパラメーターを渡し、自動生成された[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値を取得する方法について説明しています。


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
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


## 次のステップ

詳細については、[Node.js デベロッパー センター](/develop/nodejs/)を参照してください。

<!---HONumber=AcomDC_1223_2015-->