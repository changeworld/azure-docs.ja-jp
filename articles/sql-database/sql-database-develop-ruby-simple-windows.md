<properties
	pageTitle="Windows 上の Ruby と TinyTDS を使用した SQL Database への接続"
	description="Windows で実行して Azure SQL Database に接続できる Ruby コード サンプルを提供します。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Windows 上で Ruby を使用して SQL Database に接続する


<!--
Older Selector technique, with dynamic drop-down lists.
 [ A ZURE . I NCLUDE [s ql-database-develop-includes-selector-language-platform-depth](../../inclu des/sql-database-develop-includes-selector-language-platform-depth.m d)]
-->

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Windows 8.1 を実行している Windows コンピューターで実行される Ruby コード サンプルで Azure SQL Database のデータベースに接続する方法を示します。

## 前提条件

###必要なモジュールのインストール

ターミナルを開き、次をインストールします。

**1) Ruby:** コンピューターに Ruby がインストールされていない場合は、インストールを行ってください。新規の Ruby ユーザーには、Ruby 2.1.X インストーラーの使用をお勧めします。これらのインストーラーは、安定した言語を提供すると共に、互換性を有し更新済みであるパッケージ (gem) の広範なリストを提供します。[Ruby のダウンロード ページ](http://rubyinstaller.org/downloads/)に進み、適切な 2.1.x インストーラーをダウンロードします。たとえば、64 ビット コンピューターをご使用の場合は、**Ruby 2.1.6 (x64)** インストーラーをダウンロードします。<br/><br/>インストーラーがダウンロードされたら、次の操作を行います。


- インストーラーを起動するファイルをダブルクリックします。

- 使用する言語を選択し、利用規約に同意します。

- インストール設定画面で、*[パスに Ruby 実行可能ファイルを追加する]* と *[.rb および .rbw ファイルをこの Ruby インストールに関連付ける]* の横にあるチェックボックスを両方ともオンにします。


**2) DevKit:** [Ruby インストール ページ](http://rubyinstaller.org/downloads/)から DevKit をダウンロードします。

ダウンロードが完了したら、次の操作を行います。


- ファイルをダブルクリックします。ファイルを抽出する場所を指定するよう求められます。

- [...] ボタンをクリックし、"C:\\DevKit" を選択します。ほとんどの場合は、最初にこのフォルダーを作成する必要があります。それには、[新しいフォルダーの作成] をクリックします。

- [OK]、[抽出] の順にクリックして、ファイルを抽出します。


ここで、[コマンド ライン プロンプト] を開き、次のコマンドを入力します。

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Ruby と RubyGems が完全に機能する状態になりました。


**3) TinyTDS:** C:\\DevKit に進み、端末から次のコマンドを実行します。これにより、TinyTDS がコンピューターにインストールされます。

	gem inst tiny_tds --pre

### SQL Database

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## 手順 1. 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 手順 2. 接続する

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database に接続します。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## 手順 3. クエリを実行する

次のコードをコピーして、空のファイルに貼り付けます。test.rb という名前を付けます。コマンド プロンプトから次のコマンドを入力して、ファイルを実行します。

	ruby test.rb

コード サンプルでは、[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database からクエリの結果セットを取得します。この関数は、クエリを受け入れ、結果セットを返します。[result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) を使用することにより、結果セットが反復処理されます。

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## 手順 4. 行を挿入する

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

<!---HONumber=AcomDC_0316_2016-->