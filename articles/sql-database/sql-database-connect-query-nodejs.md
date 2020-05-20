---
title: Node.js を使用してデータベースを照会する
description: Azure SQL データベースに接続して T-SQL ステートメントでデータベースに照会するプログラムを Node.js で作成する方法。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768595"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>クイック スタート: Node.js を使用して Azure SQL データベースに照会する

このクイックスタートでは、Node.js を使用して Azure SQL データベースに接続し、T-SQL ステートメントを使用してデータを照会します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL データベース](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org) 関連のソフトウェア

  # <a name="macos"></a>[macOS](#tab/macos)

  「[macOS で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)」の手順 **1.2** と **1.3** を使用して、Homebrew と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  「[Ubuntu で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)」の手順 **1.2** と **1.3** を使用して、Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  # <a name="windows"></a>[Windows](#tab/windows)

  「[Windows で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)」の手順 **1.2** と **1.3** を使用して、Chocolatey と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  ---

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> オプションで、Azure SQL マネージド インスタンスの使用を選択できます。
>
> 作成して構成するには、[Azure portal](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用して、[オンサイト](sql-database-managed-instance-configure-p2s.md)または [VM](sql-database-managed-instance-configure-vm.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](sql-database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](sql-database-managed-instance-get-started-restore.md)に関するページを参照してください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインする

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。 

## <a name="create-the-project"></a>プロジェクトを作成する

コマンド プロンプトを開き、*sqltest* という名前のフォルダーを作成します。 作成したフォルダーを開き、次のコマンドを実行します。

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>データベースに照会するためのコードを追加する

1. 任意のテキスト エディターで新しいファイル (*sqltest.js*) を作成します。

1. その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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

## <a name="next-steps"></a>次のステップ

- [SQL Server 用 Microsoft Node.js ドライバー](/sql/connect/node-js/node-js-driver-for-sql-server)

- Windows/Linux/macOS における [.Net Core](sql-database-connect-query-dotnet-core.md)、[Visual Studio Code](sql-database-connect-query-vscode.md)、[SSMS](sql-database-connect-query-ssms.md) (Windows のみ) を使用した接続と照会

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)

- [.NET](sql-database-design-first-database-csharp.md) または [SSMS](sql-database-design-first-database.md) を使用した最初の Azure SQL データベースの設計
