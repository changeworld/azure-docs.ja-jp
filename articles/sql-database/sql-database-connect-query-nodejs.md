---
title: "Node.js を使用して Azure SQL Database を接続する | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる Node.js コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database: Node.js を使って接続とデータの照会を行う

このクイック スタートでは、Windows、Ubuntu Linux、Mac の各プラットフォームから [Node.js](https://nodejs.org/en/) を使用して Azure SQL Database に接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)
- [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>Node.js のインストール 

このセクションの手順では、Node.js による開発には慣れていて、Azure SQL Database は初めて使用するユーザーを想定しています。 Node.js による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**Node.js** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="mac-os"></a>**Mac OS**
次のコマンドを入力して **brew** をインストールします。これは、Mac OS X と **Node.js** に対応する使いやすいパッケージ マネージャーです。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
次のコマンドを入力して、**Node.js** と、Node.js 用のパッケージ マネージャー **npm** をインストールします。

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
[Node.js ダウンロード ページ](https://nodejs.org/en/download/)にアクセスし、必要な Windows インストーラー オプションを選択します。


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Node.js 用 tedious SQL Server ドライバーのインストール
Node.js 用の推奨されるドライバーは **[tedious](https://github.com/tediousjs/tedious)** です。 Tedious は、任意のプラットフォームの Node.js アプリケーションを対象とした、Microsoft が貢献しているオープン ソース イニシアチブです。 このチュートリアルでは、コードと、インストールする `npm` の依存関係を格納する空のディレクトリが必要です。

**tedious** ドライバーをインストールするには、ディレクトリ内で次のコマンドを実行します。

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの [**概要**] ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、[**コピーするにはクリックします**] オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じて、パスワードをリセットします。
    
## <a name="select-data"></a>データの選択

次のコードを使用して、Azure SQL データベースに対してカテゴリ単位で上位 20 の製品を照会します。 まず、tedious ドライバー ライブラリからドライバー Connect クラスと Request クラスをインポートします。 その後、構成オブジェクトを作成し、**ユーザー名**、**パスワード**、**サーバー**、および**データベース**の変数を、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 指定された `config` オブジェクトを使用して `Connection` オブジェクト作成します。 その後、`queryDatabase()` 関数を実行する `connection` オブジェクトの `connect` イベントのコールバックを定義します。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>データベースへのデータの挿入
次のコードを使用して、`insertIntoDatabase()` 関数と [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL ステートメントを使用し、SalesLT.Product テーブルに新しい製品を挿入します。 **ユーザー名**、**パスワード**、**サーバー**、**データベース**の変数を、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>データベース内のデータの更新
次のコードを使用して、`updateInDatabase()` 関数と [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL ステートメントを使用し、先ほど追加した新しい製品を更新します。 **ユーザー名**、**パスワード**、**サーバー**、**データベース**の変数を、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 このサンプルでは、前の例で挿入された製品名を使用します。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>データベースからのデータの削除
データベースからデータを削除するには、次のコードを使用します。 **ユーザー名**、**パスワード**、**サーバー**、**データベース**の変数を、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 今回は、`deleteFromDatabase()` 関数で **DELETE ステートメント**を使用します。 このサンプルでは、前の例で挿入された製品名も使用します。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>次のステップ
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Node.js ドライバー](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)
- [Visual Studio Code を使用した接続とクエリ](sql-database-connect-query-vscode.md)



