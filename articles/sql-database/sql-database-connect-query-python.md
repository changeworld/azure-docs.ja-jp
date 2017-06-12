---
title: "Python を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる Python コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5fae11119500fd3be3af3e573d45f6cc5880e037
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017

---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database: Python を使って接続とデータの照会を行う

 このクイック スタートでは、Mac OS、Ubuntu Linux、Windows の各プラットフォームから [Python](https://python.org) を使用して Azure SQL データベースに接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)
- [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Python とデータベースの通信ライブラリをインストールします。

このセクションの手順では、Python による開発には慣れていて、Azure SQL Database は初めて使用するユーザーを想定しています。 Python による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**Python** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew**、**Microsoft ODBC Driver for Mac**、**pyodbc** をインストールします。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Microsoft ODBC Driver for Linux** と **pyodbc** をインストールします。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
[Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) をインストールします (メッセージが表示されたらドライバーをアップグレードします)。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。 

その後、pip を使用して **pyodbc** をインストールします。

```cmd
pip install pyodbc==3.1.1
```

pip の使用を有効にする手順については、[ここ](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)をご覧ください。

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの [**概要**] ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、[**コピーするにはクリックします**] オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じてパスワードをリセットします。     
   
## <a name="select-data"></a>データの選択

次のコードを使用して、[pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) 関数と [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL ステートメントを使用し、カテゴリ単位で上位 20 の製品を照会します。 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 関数は、SQL Database に対するクエリから結果セットを取得するために使用されます。 この関数はクエリを受け取り、[cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html) を使用して反復処理できる結果セットを返します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

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
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>データを挿入する
次のコードを使用して、[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 関数と [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL ステートメントを使用し、SalesLT.Product テーブルに新しい製品を挿入します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>データの更新
次のコードを使用して、[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 関数と [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL ステートメントを使用し、先ほど追加した新しい製品を更新します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>データの削除
次のコードを使用して、[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 関数と [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL ステートメントを使用し、先ほど追加した新しい製品を削除します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>次のステップ

- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](/develop/python/)


