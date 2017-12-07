---
title: "Python を使用して Azure SQL Database に照会する | Microsoft Docs"
description: "このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Python で作成する方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: n
ms.devlang: python
ms.topic: quickstart
ms.date: 08/09/2017
ms.author: carlrab
ms.openlocfilehash: f7496c7b78449d5ceae861dc0daac607acdb1f84
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Python を使用して Azure SQL Database に照会する

 このクイック スタートでは、[Python](https://python.org) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタート チュートリアルを完了するには、以下のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- このクイック スタート チュートリアルに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- ご使用のオペレーティング システムに対応した Python とそれに関連するソフトウェアをインストール済みであること。

    - **MacOS**: Homebrew と Python をインストールし、ODBC ドライバーと SQLCMD をインストールした後、Python Driver for SQL Server をインストールします。 [手順 1.2、1.3、および 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) を参照してください。
    - **Ubuntu**: Python と他の必須パッケージをインストールした後、Python Driver for SQL Server をインストールします。 [手順 1.2、1.3、および 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/) を参照してください。
    - **Windows**: 最新バージョンの Python をインストールし (環境変数は自動で構成されるようになりました)、ODBC ドライバーと SQLCMD をインストールした後、Python Driver for SQL Server をインストールします。 [手順 1.2.、手順 1.3.、手順 2.1.](https://www.microsoft.com/sql-server/developer-get-started/python/windows/) を参照してください。 

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入 

1. 任意のテキスト エディターで新しいファイル (**sqltest.py**) を作成します。  

2. その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```Python
   python sqltest.py
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)

