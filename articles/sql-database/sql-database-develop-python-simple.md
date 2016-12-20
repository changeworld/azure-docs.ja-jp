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
ms.date: 10/05/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f3a4e49646063b41af5a9941f27291762f5336e


---
# <a name="connect-to-sql-database-by-using-python"></a>Python を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Python を使用して Azure SQL Database に接続し、クエリを実行する方法について説明します。 このサンプルは、Windows、Ubuntu Linux、または Mac のプラットフォームから実行できます。

## <a name="step-1-create-a-sql-database"></a>手順 1: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。 データベースを作成したら、自分の IP アドレスへのアクセスを有効にします。[使用の開始ページ](sql-database-get-started.md)の説明にあるように、ファイアウォール ルールを有効にします。

## <a name="step-2-configure-development-environment"></a>手順 2: 開発環境を設定する
### <a name="mac-os"></a>**Mac OS**
### <a name="install-the-required-modules"></a>必要なモジュールのインストール
端末を開き、次をインストールします。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql==2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**FreeTDS** と **pymssql** をインストールします。 pymssql は FreeTDS を使用して SQL Database に接続します。

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql==2.1.1

### <a name="windows"></a>**Windows**
[**ここ**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)から pymssql をインストールします。 

正しい whl ファイルを選択していることをご確認ください。 たとえば、64 ビット コンピューター上で Python 2.7 を使用している場合は、pymssql‑2.1.1‑cp27‑none‑win_amd64.whl を選択します。 .whl ファイルをダウンロードしたら、C:/Python27 フォルダーに配置します。

コマンド ラインから pip を使用し、pymssql ドライバーをインストールします。 C:/Python27 に移動し、次を実行します。

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

pip の使用を有効にする手順については、[ここ](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)をご覧ください。

## <a name="step-3-run-sample-code"></a>手順 3: サンプル コードを実行する
**sql_sample.py** という名前のファイルを作成し、その中に次のコードを貼り付けます。 これは次を使用してコマンド ラインから実行できます。

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>SQL Database に接続する
[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 関数は、SQL Database に接続するために使用します。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>SQL SELECT ステートメントの実行
[Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 関数は、SQL Database に対するクエリから結果セットを取得するために使用できます。 この関数は基本的に任意のクエリを受け取り、 [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) を使用して反復処理できる結果セットを返します。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する
SQL Database では、[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトを使用して、[プライマリ キーの値](https://msdn.microsoft.com/library/ms179610.aspx)を自動生成できます。 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>トランザクション
このコード例は、以下のトランザクションの使用について示します。

* トランザクションの開始
* データの挿入
* トランザクションをロールバックして、挿入を元に戻す 

sql_sample.py 内に次のコードを貼り付けます。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
*  [Microsoft Python Driver for SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* [Python デベロッパー センター](/develop/python/)の参照

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
*  [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


