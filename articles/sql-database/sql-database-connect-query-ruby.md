---
title: Ruby を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースを照会するプログラムを Ruby で作成する方法について説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f86c53465636f82cf36d5099699fc9e6efeb55a5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704659"
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Ruby を使用して Azure SQL Database に照会する

このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを [Ruby](https://www.ruby-lang.org) を使って作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- このクイック スタートに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- ご使用のオペレーティング システムに対応した Ruby とそれに関連するソフトウェアをインストール済みであること。
    - **MacOS**: Homebrew をインストールし、rbenv と ruby-build をインストールした後、Ruby と FreeTDS を順にインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.、手順 1.5.](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) を参照してください。
    - **Ubuntu**: Ruby の前提条件をインストールし、rbenv と ruby-build をインストールした後、Ruby と FreeTDS を順にインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.、手順 1.5.](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) を参照してください。

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> このチュートリアルを実行するコンピューターのパブリック IP アドレスに対してファイアウォール規則を設定しておく必要があります。 別のコンピューターから実行する場合または別のパブリック IP アドレスがある場合は、[Azure Portal でサーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)を作成してください。 

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. 任意のテキスト エディターで新しいファイル (**sqltest.rb**) を作成します。

2. その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

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

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```bash
   ruby sqltest.rb
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。


## <a name="next-steps"></a>次の手順
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [TinyTDS の GitHub リポジトリ](https://github.com/rails-sqlserver/tiny_tds)
- [TinyTDS についての問題の報告と質問](https://github.com/rails-sqlserver/tiny_tds/issues)
- [SQL Server 用 Ruby ドライバー](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
