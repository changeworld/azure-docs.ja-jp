<properties
	pageTitle="Mac OS X (Yosemite) 上の TinyTDS と Ruby を使用して SQL Database に接続します。"
	description="Mac OS X (Yosemite) で実行して Azure SQL Database に接続できる Ruby コード サンプルを提供します。"
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="andrela"/>


# Mac OS X (Yosemite) での Ruby を使用した SQL Database への接続

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Yosemite を実行している Mac コンピューターで実行される Ruby コード サンプルで Azure SQL Database のデータベースに接続する方法を示します。

## 手順 1: 開発環境を設定する

[TinyTDS Ruby Driver for SQL Server 使用の前提条件](https://msdn.microsoft.com/library/mt711041.aspx#Mac)

## 手順 2: SQL Database を作成する

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## 手順 3: 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 手順 4: 接続する

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database に接続します。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## 手順 5: クエリを実行する

[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database からクエリの結果セットを取得します。この関数は、クエリを受け入れ、結果セットを返します。[result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) を使用することにより、結果セットが反復処理されます。

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("select * from SalesLT.Product")
    results.each do |row|
    puts row
    end

## 手順 6: 行を挿入する

この例では、[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) ステートメントを安全に実行し、[SQL インジェクション](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) の脆弱性からアプリケーションを保護するパラメーターを渡し、自動生成された[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値を取得する方法について説明しています。


Azure で TinyTDS を使用するには、いくつかの `SET` ステートメントを実行して現在のセッションが特定の情報を処理する方法を変更することをお勧めします。推奨 `SET` ステートメントは、コード サンプルに用意されています。たとえば、`SET ANSI_NULL_DFLT_ON` は、列の NULL 値が許容されることが明示的に宣言されていない場合でも、作成された新しい列が NULL 値を持つことを許可します。

Microsoft SQL Server の [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 形式に合うように、[strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 関数を使用して対応する日付時刻形式にキャストします。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end

<!---HONumber=AcomDC_0330_2016------>