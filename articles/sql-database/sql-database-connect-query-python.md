---
title: Python を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Python で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271829"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>クイック スタート:Python を使用して Azure SQL Database に照会する

 このクイック スタートでは、[Python](https://python.org) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。 SDK の詳細については、[リファレンス](https://docs.microsoft.com/python/api/overview/azure/sql) ドキュメント、[pyodbc GitHub リポジトリ](https://github.com/mkleehammer/pyodbc/wiki/)、[pyodbc サンプル](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)を確認してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- このクイック スタートに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)。
  
- ご使用のオペレーティング システムに対応した Python とそれに関連するソフトウェア:
  
  - **MacOS**:Homebrew と Python をインストールし、ODBC ドライバーと SQLCMD をインストールした後、SQL Server 用の Python ドライバーをインストールします。 [SQL Server を使用した Python アプリの作成](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)に関する記事の手順 1.2、1.3、および 2.1 を参照してください。 詳しくは、「[Linux および macOS に Microsoft ODBC Driver インストールする](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)」をご覧ください。
    
  - **Ubuntu**:`sudo apt-get install python python-pip gcc g++ build-essential` を使用して、Python とその他の必要なパッケージをインストールします。 ODBC ドライバー、SQLCMD、および SQL Server 用 Python ドライバーをダウンロードしてインストールします。 手順については、「[pyodbc Python 開発用に開発環境を構成する](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)」をご覧ください。
    
  - **Windows**:Python、ODBC ドライバー、SQLCMD、および SQL Server 用 Python ドライバーをインストールします。 手順については、「[pyodbc Python 開発用に開発環境を構成する](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)」をご覧ください。

## <a name="get-sql-server-connection-information"></a>SQL Server 接続情報を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する 

1. 任意のテキスト エディターで新しいファイル (*sqltest.py*) を作成します。  
   
1. 次のコードを追加します。 \<server>、\<database>、\<username>、\<password> を独自の値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
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

1. 上位 20 カテゴリ/製品の行が返されることを確認し、コマンド ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)

