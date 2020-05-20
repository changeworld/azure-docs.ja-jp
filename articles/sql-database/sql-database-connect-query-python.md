---
title: Python を使用してデータベースを照会する
description: このトピックでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Python で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768572"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>クイック スタート: Python を使用して Azure SQL データベースに照会する

このクイックスタートでは、Python を使用して Azure SQL データベースに接続し、T-SQL ステートメントを使用してデータを照会します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL データベース](sql-database-single-database-get-started.md)
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

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> オプションで、Azure SQL マネージド インスタンスの使用を選択できます。
>
> 作成して構成するには、[Azure portal](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用して、[オンサイト](sql-database-managed-instance-configure-p2s.md)または [VM](sql-database-managed-instance-configure-vm.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](sql-database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](sql-database-managed-instance-get-started-restore.md)に関するページを参照してください。

Python と Azure SQL データベースの詳細については、「[Python 用 Azure SQL Database ライブラリ](/python/api/overview/azure/sql)」、[pyodbc リポジトリ](https://github.com/mkleehammer/pyodbc/wiki/)、および [pyodbc のサンプル](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)を参照してください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインする

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

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

## <a name="next-steps"></a>次のステップ

- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)

