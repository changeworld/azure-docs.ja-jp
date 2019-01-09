---
title: Ruby を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースを照会するプログラムを Ruby で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993911"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>クイック スタート:Ruby を使用して Azure SQL Database に照会する

このクイック スタートでは、[Ruby](https://www.ruby-lang.org) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータのクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- このクイック スタートに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal-firewall.md)。
  
- ご使用のオペレーティング システムに対応した Ruby とそれに関連するソフトウェア:
  
  - **MacOS**: Homebrew、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on macOS (macOS での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Ubuntu**:Ruby に対する前提条件、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on Ubuntu (Ubuntu での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Windows**:Ruby、Ruby Devkit、TinyTDS をインストールします。 「[Ruby 開発用に開発環境を構成する](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)」をご覧ください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する

1. 任意のテキストまたはコード エディターで新しいファイル (*sqltest.rb*) を作成します。
   
1. 次のコードを追加します。 `<server>`、`<database>`、`<username>`、`<password>` を Azure SQL データベースからの値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
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
   
1. データベースの上位 20 のカテゴリ/製品行が返されていることを確認します。 

## <a name="next-steps"></a>次の手順
- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)。
- [TinyTDS の GitHub リポジトリ](https://github.com/rails-sqlserver/tiny_tds)。
- [TinyTDS についての問題の報告と質問](https://github.com/rails-sqlserver/tiny_tds/issues)。
- [SQL Server 用 Ruby ドライバー](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
