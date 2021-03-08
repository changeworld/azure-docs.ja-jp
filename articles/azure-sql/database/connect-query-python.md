---
title: Python を使用してデータベースを照会する
description: このトピックでは、Azure SQL Database のデータベースに接続して Transact-SQL ステートメントでデータベースにクエリを実行するプログラムを Python で作成する方法について説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: 8fb6d319cacf85630b2c400cd18d14487725f925
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703971"
---
# <a name="quickstart-use-python-to-query-a-database"></a>クイックスタート: Python を使用してデータベースを照会する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

このクイックスタートでは、Python を使用して Azure SQL Database、Azure SQL Managed Instance、または Synapse SQL データベースに接続し、T-SQL ステートメントを使用してデータに対してクエリを実行します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- クエリを実行するデータベース。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 および関連するソフトウェア

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew と Python、ODBC ドライバーと SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[macOS での SQL Server を使用した Python アプリの作成](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)に関するページの手順 **1.2**、**1.3**、および **2.1** を使用します。

  詳細については、[macOS の Microsoft ODBC ドライバー](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Python とその他の必要なパッケージをインストールするには、`sudo apt-get install python python-pip gcc g++ build-essential` を使用します。

  ODBC ドライバー、SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[pyodbc Python 開発用の環境の構成](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)に関するページを参照してください。

  詳細については、[Linux の Microsoft ODBC ドライバー](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

  # <a name="windows"></a>[Windows](#tab/windows)

  Python、ODBC ドライバー、SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[pyodbc Python 開発用の環境の構成](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)に関するページを参照してください。

  詳細については、[Microsoft ODBC ドライバー](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

---
Python と Azure SQL Database のデータベースの詳細については、「[Python 用 Azure SQL Database ライブラリ](/python/api/overview/azure/sql)」、[pyodbc リポジトリ](https://github.com/mkleehammer/pyodbc/wiki/)、および [pyodbc のサンプル](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)を参照してください。

## <a name="create-code-to-query-your-database"></a>データベースに対してクエリを実行するコードを作成する 

1. 任意のテキスト エディターで新しいファイル (*sqltest.py*) を作成します。  
   
1. 次のコードを追加します。 「前提条件」セクションから接続情報を入手し、\<server>、\<database>、\<username>、\<password> を実際の値に置き換えてください。
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```cmd
   python sqltest.py
   ```

1. データベースとその照合順序が返されていることを確認し、コマンド ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)
- [SQL Server 用 Microsoft Python ドライバー](/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)
