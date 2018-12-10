---
title: PHP を使用して Azure SQL データベースに照会する | Microsoft Docs
description: Azure SQL データベースに接続して T-SQL ステートメントでデータベースに照会するプログラムを PHP で作成する方法。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: be3ac9fab6c89c65ad9673811e108cefe2c80d00
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724249"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>クイック スタート: PHP を使用して Azure SQL Database に照会する

この記事では、[PHP](http://php.net/manual/en/intro-whatis.php) を使用して Azure SQL データベースに接続する方法を紹介します。 その後、T-SQL ステートメントを使用してデータを照会することができます。

## <a name="prerequisites"></a>前提条件

このサンプルを完了するには、次の前提条件を満たしている必要があります。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- ご使用のコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)

- ご使用のオペレーティング システムに対応した、以下の PHP 関連のソフトウェアをインストール済みであること。

    - **MacOS**: Homebrew と PHP、ODBC ドライバーと SQLCMD をインストールした後、PHP Driver for SQL Server をインストールします。 [手順 1.2.、手順 1.3.、手順 2.1.](https://www.microsoft.com/sql-server/developer-get-started/php/mac/) を参照してください。

    - **Ubuntu**: PHP と他の必須パッケージをインストールした後、PHP Driver for SQL Server をインストールします。 [手順 1.2. と手順 2.1.](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/) を参照してください。

    - **Windows**: IIS Express と Chocolatey 用の PHP をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/php/windows/) を参照してください。

## <a name="get-database-connection"></a>データベース接続を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>データベースに照会するためのコードを追加する

1. 任意のテキスト エディターで新しいファイル (*sqltest.php*) を作成します。  

1. その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
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

1. コマンド プロンプトでアプリを実行します。

   ```bash
   php sqltest.php
   ```

1. 先頭から 20 行が返されることを確認して、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)

- [SQL Server 用 Microsoft PHP ドライバー](https://github.com/Microsoft/msphpsql/)

- [問題の報告と質問](https://github.com/Microsoft/msphpsql/issues)

- [再試行ロジックの例: PHP を使用して SQL に弾性的に接続する](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
