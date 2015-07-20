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
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Windows 上で Node.js を使用して、SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。Node.js プログラムは、Windows クライアント コンピューター上で実行されます。接続の管理には、msnodesql ドライバーが使用されます。


## 必要条件


次のソフトウェア アイテムが、クライアント開発コンピューターに存在する必要があります。


-  Node.js – [バージョン 0.8.9 (32 ビット バージョン)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/)。スクロールして、Windows x64 Installer 64 bit ではなく、Windows Installer for 32 bit x86 のダウンロードをクリックします。
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/) は、x86 か x64 のいずれかのインストーラーです。 
- [Visual C 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) - 無償版が Microsoft から無料で公開されています。
- SQL Server Native Client 11.0 - [SQL Server 2012 Feature Pack](http://www.microsoft.com/download/details.aspx?id=29065) にある Microsoft SQL Server 2012 Native Client として利用できます。


### 必要なモジュールのインストール


**Cmd.exe** コマンド ライン ウィンドウで、msnodesql のあるディレクトリに移動します。表示された順序で、次のコマンドを入力します。


	npm install msnodesql
	npm install -g node-gyp


node-gyp がインストールされたら、ディレクトリ *YourProjectDirectory* に移動し、次に **node_modules\\msnodesql** に移動します。**cmd.exe** ウィンドウで、次のコマンドを発行します。


	node-gyp configure 
	node-gyp build


次に、ディレクトリ **build\\release** に移動します。**sqlserver.node** ファイルをコピーし、**msnodesql\\lib** ディレクトリに貼り付けます。必要な場合は、古いファイルを置き換えます。

[チーム ブログ](http://blogs.msdn.com/b/sqlphp/archive/2015/05/12/getting-started-with-node-js-and-microsoft-sql-server-and-azure-sql-database.aspx)と[ビデオ](https://www.youtube.com/watch?v=kQo_L-D_zk8)をチェックして、上記の要件をインストールおよびセットアップする方法を確認してください。


### データベースを作成し、接続文字列を取得します。
 
「[トピックの開始](sql-database-get-started.md)」ページで、サンプル データベースの作成方法と接続文字列を取得する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## SQL Database に接続する


- プロジェクト ディレクトリ内にある .js ファイルの次のコードをコピーします。


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- 次のコマンドを発行して .js ファイルを実行します。


		node index.js


## SQL SELECT ステートメントの実行


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## 行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## トランザクション


メソッド **conn.beginTransactions** は、Azure SQL Database では機能しません。代わりに、次のコード サンプルに従って、SQL Databaseでトランザクションを実行します。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## ストアド プロシージャ


このコード サンプルを機能させるには、パラメーターを入力しないストアド プロシージャを持っているか、作成する必要があります。ストアド プロシージャを作成するには、SQL Server Management Studio (SSMS.exe) などのツールを使用します。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

 

<!---HONumber=July15_HO2-->