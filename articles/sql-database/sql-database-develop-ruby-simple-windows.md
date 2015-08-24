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
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Windows 上で Ruby を使用して SQL Database に接続する

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Windows 8.1 を実行している Windows コンピューターで実行される Ruby コード サンプルで Azure SQL Database のデータベースに接続する方法を示します。

## 必要なモジュールのインストール

ターミナルを開き、次をインストールします。

**1) Ruby:** コンピューターに Ruby がインストールされていない場合は、インストールを行ってください。新規の Ruby ユーザーには、Ruby 2.1.X インストーラーの使用をお勧めします。これらのインストーラーは、安定した言語を提供すると共に、互換性を有し更新済みであるパッケージ (gem) の広範なリストを提供します。[Ruby のダウンロード ページ]()に進み、適切な 2.1.x インストーラーをダウンロードします。たとえば、64 ビット コンピューターをご使用の場合は、**Ruby 2.1.6 (x64)** インストーラーをダウンロードします。<br/><br/>インストーラーがダウンロードされたら、次の操作を行います。


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

## データベースを作成し、接続文字列を取得する

Ruby サンプルは、`AdventureWorks` サンプル データベースに依存します。`AdventureWorks` をまだお持ちでない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」で作成方法を確認できます。

このトピックでは、データベース接続文字列を取得する方法についても説明します。

## SQL Database に接続する

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database に接続します。

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## SELECT ステートメントを実行し、結果セットを取得する

次のコードをコピーして、空のファイルに貼り付けます。test.rb という名前を付けます。コマンド プロンプトから次のコマンドを入力して、ファイルを実行します。

	ruby test.rb

コード サンプルでは、[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 関数を使用して、SQL Database からクエリの結果セットを取得します。この関数は、クエリを受け入れ、結果セットを返します。[result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) を使用することにより、結果セットが反復処理されます。

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

<!---HONumber=August15_HO7-->