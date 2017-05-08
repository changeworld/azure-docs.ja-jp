---
title: "Ruby を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続とデータの照会に使用できる Ruby コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/17/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c8700b16f91f014205acb93d6b57f9b972546268
ms.lasthandoff: 04/21/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database: Ruby を使って接続とデータの照会を行う

このクイック スタートでは、Mac OS と Ubuntu Linux のプラットフォームから [Ruby](https://Ruby.org) を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Ruby とデータベースの通信ライブラリをインストールする

このセクションの手順では、Ruby による開発には慣れているが、Azure SQL Database を初めて使用するユーザーを想定しています。 Ruby による開発の経験がない場合は、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/en-us/sql-server/developer-get-started/)」に移動し、**Ruby** を選択してから、使用しているオペレーティング システムを選択します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、Ruby スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew**、**FreeTDS**、および **TinyTDS** をインストールします。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、Ruby スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**FreeTDS** と **TinyTDS** をインストールします。

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>接続情報の取得

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの [**概要**] ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、[**コピーするにはクリックします**] オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じて、パスワードをリセットします。
    

## <a name="select-data"></a>データの選択
次のコードを使用して、[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL ステートメントを使用し、カテゴリ単位で上位 20 の製品を照会します。 TinyTDS::Client 関数は、クエリを受け入れて結果セットを返します。 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)を使用することにより、結果セットが反復処理されます。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>データを挿入する
次のコードを使用し、[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL ステートメントを使用して、SalesLT.Product テーブルに新しい製品を挿入します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

この例では、INSERT ステートメントを安全に実行し、[SQL インジェクション](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)の脆弱性からアプリケーションを保護するパラメーターを渡し、自動生成された[プライマリ キー](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)値を取得する方法について説明しています。    
  
Azure で TinyTDS を使用するには、いくつかの `SET` ステートメントを実行して現在のセッションが特定の情報を処理する方法を変更することをお勧めします。 推奨 `SET` ステートメントは、コード サンプルに用意されています。 たとえば、 `SET ANSI_NULL_DFLT_ON` は、列の NULL 値が許容されることが明示的に宣言されていない場合でも、作成された新しい列が NULL 値を持つことを許可します。  
  
Microsoft SQL Server の [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) 形式に合うように、[strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 関数を使用して対応する日付時刻形式にキャストします。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>データの更新
次のコードを使用し、[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL ステートメントで、先ほど追加した新しい製品を更新します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>データの削除
次のコードを使用し、[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL ステートメントで、先ほど追加した新しい製品を削除します。 サーバー、データベース、ユーザー名、パスワードのパラメーターを、AdventureWorksLT サンプル データでデータベースを作成したときに指定した値に置き換えます。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>次のステップ

- [TinyTDS](https://github.com/rails-sqlserver/tiny_tds) の GitHub レポジトリ。
- [問題/質問を登録します](https://github.com/rails-sqlserver/tiny_tds/issues)。        
- [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/) の詳細。
- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- Visual Studio を使用して接続とデータの照会を行うには、[Visual Studio Code を使った接続とデータの照会](sql-database-connect-query-vscode.md)に関するページを参照してください。
- .NET を使用して接続とデータの照会を行うには、[.NET を使った接続とデータの照会](sql-database-connect-query-dotnet.md)に関するページを参照してください。
- PHP を使用して接続とデータの照会を行うには、[PHP を使った接続とデータの照会](sql-database-connect-query-php.md)に関するページを参照してください。
- Node.js を使用して接続とデータの照会を行うには、[Node.js を使った接続とデータの照会](sql-database-connect-query-nodejs.md)に関するページを参照してください。
- Java を使用して接続とデータの照会を行うには、[Java を使った接続とデータの照会](sql-database-connect-query-java.md)に関するページを参照してください。
- Python を使用して接続とデータの照会を行うには、[Python を使った接続とデータの照会](sql-database-connect-query-python.md)に関するページを参照してください。

