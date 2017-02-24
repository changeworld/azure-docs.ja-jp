---
title: "Python を使用して SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続に使用できる Python コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2793ddb1c903f6732a193276a2d804192b7ab53b
ms.openlocfilehash: 86524dd1a73df3b60245cb664c0e17a63df00763


---
# <a name="connect-to-sql-database-by-using-python"></a>Python を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Python を使用して Azure SQL Database に接続し、クエリを実行する方法について説明します。 このサンプルは、Windows、Ubuntu Linux、または Mac のプラットフォームから実行できます。

## <a name="step-1-create-a-sql-database"></a>手順 1: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。 データベースを作成したら、自分の IP アドレスへのアクセスを有効にします。[使用の開始ページ](sql-database-get-started.md)の説明にあるように、ファイアウォール ルールを有効にします。

## <a name="step-2-configure-development-environment"></a>手順 2: 開発環境を設定する
### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew**、**Microsoft ODBC Driver for Mac**、**pyodbc** をインストールします。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Microsoft ODBC Driver for Linux** と **pyodbc** をインストールします。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
[Microsoft ODBC Driver 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339) をインストールします。 pyodbc は、Linux 上で Microsoft ODBC Driver を使用して SQL データベースに接続します。 

その後、pip を使用して pyodbc をインストールします

```
pip install pyodbc==3.1.1
```

pip の使用を有効にする手順については、[ここ](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)をご覧ください。

## <a name="step-3-run-sample-code"></a>手順 3: サンプル コードを実行する
**sql_sample.py** という名前のファイルを作成し、その中に次のコードを貼り付けます。 これは次を使用してコマンド ラインから実行できます。

```
python sql_sample.py
```

### <a name="connect-to-your-sql-database"></a>SQL Database に接続する
[pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) 関数は、SQL Database に接続するために使用します。

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
if row:
    print row
```

### <a name="execute-an-sql-select-statement"></a>SQL SELECT ステートメントの実行
[Cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 関数は、SQL Database に対するクエリから結果セットを取得するために使用できます。 この関数は基本的に任意のクエリを受け取り、 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html) を使用して反復処理できる結果セットを返します。

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1]) + " " + str(row[2])
    row = cursor.fetchone()
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する
SQL Database では、[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトを使用して、[プライマリ キーの値](https://msdn.microsoft.com/library/ms179610.aspx)を自動生成できます。 

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print "Inserted Product ID : " +str(row[0])
    row = cursor.fetchone()
```

### <a name="transactions"></a>トランザクション
このコード例は、以下のトランザクションの使用について示します。

* トランザクションの開始
* データの挿入
* トランザクションをロールバックして、挿入を元に戻す 

sql_sample.py 内に次のコードを貼り付けます。

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("BEGIN TRANSACTION")
cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
cnxn.rollback()
```

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
* [Python デベロッパー センター](/develop/python/)の参照

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Feb17_HO2-->


