---
title: PHP を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを PHP で作成する方法について説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: php
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 8fe343587336ff22f82ed0d1ef700fc56c86f577
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561096"
---
# <a name="use-php-to-query-an-azure-sql-database"></a>PHP を使用して Azure SQL Database に照会する

このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを [PHP](http://php.net/manual/en/intro-whatis.php) を使って作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- このクイック スタートに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- ご使用のオペレーティング システムに対応した PHP とそれに関連するソフトウェアをインストール済みであること。

    - **MacOS**: Homebrew と PHP をインストールし、ODBC ドライバーと SQLCMD をインストールした後、PHP Driver for SQL Server をインストールします。 [手順 1.2、1.3、および 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/) を参照してください。
    - **Ubuntu**:  PHP と他の必須パッケージをインストールした後、PHP Driver for SQL Server をインストールします。 [手順 1.2 および 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/) を参照してください。
    - **Windows**: 最新バージョンの PHP for IIS Express、最新バージョンの Microsoft Drivers for SQL Server in IIS Express、Chocolatey、ODBC ドライバー、SQLCMD をインストールします。 [手順 1.2 および 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/) を参照してください。    

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. 任意のテキスト エディターで新しいファイル (**sqltest.php**) を作成します。  

2. その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

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

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```php
   php sqltest.php
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft PHP ドライバー](https://github.com/Microsoft/msphpsql/)
- [問題の報告と質問](https://github.com/Microsoft/msphpsql/issues)
- [再試行ロジックの例: PHP を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

