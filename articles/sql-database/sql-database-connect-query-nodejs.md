---
title: Node.js を使用して Azure SQL Database に照会する | Microsoft Docs
description: Azure SQL データベースに接続して T-SQL ステートメントでデータベースに照会するプログラムを Node.js で作成する方法。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 22c783574897076b697b4b2bb3e8fc1da284dcae
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724674"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>クイック スタート: Node.js を使用して Azure SQL Database に照会する

この記事では、[Node.js](https://nodejs.org) を使用して Azure SQL データベースに接続する方法を紹介します。 その後、T-SQL ステートメントを使用してデータを照会することができます。

## <a name="prerequisites"></a>前提条件

このサンプルを完了するには、次の前提条件を満たしている必要があります。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- ご使用のコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)

- ご使用のオペレーティング システムに対応した Node.js 関連ソフトウェア:

  - **MacOS**: Homebrew と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) を参照してください。
  
  - **Ubuntu**: Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) を参照してください。
  
  - **Windows**: Chocolatey と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) を参照してください。

## <a name="get-database-connection"></a>データベース接続を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> このチュートリアルを実行するコンピューターのパブリック IP アドレスに対してファイアウォール規則を設定しておく必要があります。 別のコンピューターから実行する場合または別のパブリック IP アドレスがある場合は、[Azure portal を使用してサーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)を作成してください。

## <a name="create-the-project"></a>プロジェクトを作成する

コマンド プロンプトを開き、*sqltest* という名前のフォルダーを作成します。 作成したフォルダに移動し、次のコマンドを実行します。

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>データベースに照会するためのコードを追加する

1. 任意のテキスト エディターで新しいファイル (*sqltest.js*) を作成します。

1. その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
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

> [!NOTE]
> このコード例では、Azure SQL に **AdventureWorksLT** サンプル データベースを使用します。

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトでプログラムを実行します。

    ```bash
    node sqltest.js
    ```

1. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- [SQL Server 用 Microsoft Node.js ドライバー](/sql/connect/node-js/node-js-driver-for-sql-server)

- Windows/Linux/macOS における [.Net Core](sql-database-connect-query-dotnet-core.md)、[Visual Studio Code](sql-database-connect-query-vscode.md)、[SSMS](sql-database-connect-query-ssms.md) (Windows のみ) を使用した接続と照会

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)

- [.NET](sql-database-design-first-database-csharp.md) または [SSMS](sql-database-design-first-database.md) を使用した最初の Azure SQL データベースの設計
