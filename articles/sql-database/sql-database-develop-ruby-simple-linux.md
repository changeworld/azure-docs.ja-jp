<properties 
	pageTitle="Ubuntu 上の Ruby と TinyTDS を使用した SQL Database への接続" 
	description="Azure SQL Database に接続するために、Ubuntu Linux クライアントとして実行できる Ruby のコード サンプルを提供します。"
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
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Ubuntu Linux 上の Ruby を使用した SQL Database への接続

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Ubuntu Linux クライアント コンピューター上で実行される Ruby のコード サンプルを示し、Azure SQL Database のデータベースに接続する方法について説明します。

## 必要なモジュールのインストール

端末を開き、FreeTDS をコンピューターにインストールします (まだインストールしていない場合)。
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin

コンピューターで FreeTDS を構成してから、Ruby をコンピューターにインストールします (まだインストールしていない場合)。
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

署名に関する問題がある場合は、次のコマンドを実行してください。

    command curl -sSL https://rvm.io/mpapis.asc | gph --import - 

署名に関する問題がない場合は、次のコマンドを実行してください。

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

バージョン 2.1.2 または Ruby の VM が実行されていることを確認します。

次に、TinyTDS をインストールします。

    gem install tiny_tds

## データベースを作成し、接続文字列を取得する

Ruby のサンプルは、AdventureWorks サンプル データベースに依存します。AdventureWorks をまだお持ちでない場合、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」で作成方法を確認できます。

このトピックでは、データベース接続文字列を取得する方法についても説明します。

## SQL Database に接続する

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database に接続します。

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## SELECT ステートメントを実行し、結果セットを取得する

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

## 行を挿入する、パラメーターを渡す、生成されたプライマリ キー値を取得する

コード サンプル:

- 行に挿入される値のパラメーターを渡します。
- 行を挿入します。
- プライマリ キー対して生成された値を取得します。

SQL Database では、[IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx) オブジェクトを使用して、[プライマリ キーの値](http://msdn.microsoft.com/library/ms179610.aspx)を自動生成できます。

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

<!---HONumber=July15_HO4-->