---
title: "PHP を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる PHP コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 05/07/2017
ms.author: meetb;carlrab;sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 2c0dc1bef0bf2b7b78e8858943ad6e0ede214681
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL Database: PHP を使って接続とデータの照会を行う

このクイック スタートでは、Mac OS、Ubuntu Linux、Windows の各プラットフォームから [PHP](http://php.net/manual/en/intro-whatis.php) を使用して Azure SQL Database に接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="install-php-and-database-communications-software"></a>PHP とデータベースの通信ソフトウェアのインストール

このセクションの手順では、PHP による開発には慣れていて、Azure SQL Database は初めて使用するユーザーを想定しています。 PHP による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**PHP** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、次のコマンドを入力して、**brew**、**Microsoft ODBC Driver for Mac**、および **Microsoft PHP Drivers for SQL Server** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
次のコマンドを入力して、**Microsoft ODBC Driver for Linux** および **Microsoft PHP Drivers for SQL Server** をインストールします。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- [WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) から PHP 7.1.1 (x64) をインストールします 
- [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) をインストールします。 
- [Microsoft PHP Driver for SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) の非スレッド セーフな dll をダウンロードし、バイナリを PHP\v7.x\ext フォルダーに配置します。
- 次に、php.ini (C:\Program Files\PHP\v7.1\php.ini) ファイルを編集して、dll への参照を追加します。 For example:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

この時点で、dll は PHP に登録されています。

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの [**概要**] ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、[**コピーするにはクリックします**] オプションが表示されます。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じてパスワードをリセットします。     
    
## <a name="select-data"></a>データの選択
次のコードを使用して、カテゴリ別の上位 20 の製品を照会します。[sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 関数と [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL ステートメントを使用します。 sqlsrv_query 関数は、SQL Database に対するクエリから結果セットを取得するために使います。 この関数は基本的に任意のクエリを受け取り、 [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) を使用して反復処理できる結果セットを返します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>データを挿入する
次のコードを使用して、SalesLT.Product テーブルに新しい製品を挿入します。このコードでは、[sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 関数と [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL ステートメントを使用します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>データの更新
次のコードを使用し、[sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 関数と [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを更新します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>データの削除
次のコードを使用して、先ほど追加した新しい製品を削除します。[sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 関数と [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL ステートメントを使用します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>次のステップ
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft PHP ドライバー](https://github.com/Microsoft/msphpsql/)
- [問題の報告/質問](https://github.com/Microsoft/msphpsql/issues)


