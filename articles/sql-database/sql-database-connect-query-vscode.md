---
title: "VS Code: Azure SQL Database に接続してデータを照会する | Microsoft Docs"
description: "Visual Studio Code を使用して Azure SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。"
metacanonical: 
keywords: "SQL Database への接続"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 5b623c78f8b8eac846c5ca244f1e0b25ee4f400f
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Visual Studio Code を使って接続とデータの照会を行う

[Visual Studio Code](https://code.visualstudio.com/docs) は、Linux、macOS、Windows で使用できるグラフィカル コード エディターです。Microsoft SQL Server、Azure SQL Database、SQL Data Warehouse のデータを照会するために、[mssql 拡張機能](https://aka.ms/mssql-marketplace)など、各種の拡張機能をサポートしています。 このクイック スタートでは、Visual Studio Code を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

開始する前に、必ず最新バージョンの [Visual Studio Code](https://code.visualstudio.com/Download) をインストールして [mssql 拡張機能](https://aka.ms/mssql-marketplace)を読み込んでおいてください。 mssql 拡張機能のインストール ガイダンスについては、「[Install VS Code (VS コードのインストール)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)」と「[mssql for Visual Studio Code (Visual Studio Code 用 mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)」を参照してください。 

## <a name="configure-vs-code-mac-os-only"></a>VS コードの構成 (Mac OS のみ)

### <a name="mac-os"></a>**Mac OS**
macOS では、mssql 拡張機能で使用される DotNet Core の前提条件として、OpenSSL をインストールする必要があります。 使用するターミナルを開き、次のコマンドを入力して、**brew** と **OpenSSL** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

## <a name="get-connection-information"></a>接続情報の取得

Azure Portal で、Azure SQL Database サーバーの完全修飾サーバー名を取得します。 その完全修飾サーバー名は、Visual Studio Code でのサーバーへの接続に使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます。

   ![接続情報](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じてパスワードをリセットします。 

## <a name="set-language-mode-to-sql"></a>言語モードを SQL に設定

Visual Studio Code で言語モードを **[SQL]** に設定し、mssql コマンドと T-SQL IntelliSense を有効にします。

1. 新しい Visual Studio Code ウィンドウを開きます。 

2. ステータス バーの右下隅の **[プレーン テキスト]** をクリックします。
3. 表示された **[言語モードの選択]** ドロップダウン メニューで「**SQL**」と入力し、**Enter** キーを押して、言語モードを SQL に設定します。 

   ![SQL 言語モード](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>SQL Database 論理サーバーのデータベースに接続する

Visual Studio Code を使用して、Azure SQL Database サーバーに対する接続を確立します。

> [!IMPORTANT]
> 続行する前に、サーバー、データベース、およびログイン情報が準備できていることを確認します。 接続プロファイル情報の入力を開始した後は、Visual Studio Code からフォーカスを移動すると、接続プロファイルの作成をやり直さなければならなくなります。
>

1. VS Code で、**Ctrl + Shift + P** キー (または **F1** キー) を押してコマンド パレットを開きます。

2. 「**sqlcon**」と入力して **Enter** キーを押します。

3. **Enter** キーを押して **[接続プロファイルの作成]** を選択します。 これで、SQL Server インスタンスの接続プロファイルが作成されます。

4. プロンプトに従って、新しい接続プロファイルの接続プロパティを指定します。 それぞれの値を指定したら、**Enter** キーを押して続行します。 

   次の表で、接続プロファイルのプロパティについて説明します。

   | Setting | Description |
   |-----|-----|
   | **[サーバー名]** | 完全修飾サーバー名を入力します (**mynewserver20170313.database.windows.net** など) |
   | **[データベース名]** | データベース名を入力します (**mySampleDatabase** など) |
   | **認証** | [SQL ログイン] を選択します |
   | **ユーザー名** | サーバー管理者アカウントを入力します |
   | **[パスワード (SQL ログイン)]** | サーバー管理者アカウントのパスワードを入力します | 
   | **[パスワードを保存しますか?]** | **[はい]** または **[いいえ]** を選択します |
   | **(省略可能) [このプロファイルの名前を入力してください]** | 接続プロファイルの名前を入力します (**mySampleDatabase**) 

5. **Esc** キーを押して、プロファイルが作成され、接続が確立されたことを示す情報メッセージを閉じます。

6. ステータス バーで接続を確認します。

   ![接続の状態](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>データのクエリを実行する

[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを照会します。

1. **エディター** ウィンドウで、空のクエリ ウィンドウに次のクエリを入力します。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. **Ctrl + Shift + E** キーを押して、Product と ProductCategory のテーブルからデータを取得します。

    ![クエリ](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>データを挿入する

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースにデータを挿入します。

1. **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. **Ctrl + Shift + E** キーを押して、新しい行を Product テーブルに挿入します。

## <a name="update-data"></a>データの更新

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを更新します。

1.  **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl + Shift + E** キーを押して、Product テーブルの指定した行を更新します。

## <a name="delete-data"></a>データの削除

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを削除します。

1. **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl + Shift + E** キーを押して、Product テーブルの指定した行を削除します。

## <a name="next-steps"></a>次のステップ

- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- .NET を使用して接続とデータの照会を行うには、[.NET を使った接続とデータの照会](sql-database-connect-query-dotnet.md)に関するページを参照してください。
- PHP を使用して接続とデータの照会を行うには、[PHP を使った接続とデータの照会](sql-database-connect-query-php.md)に関するページを参照してください。
- Node.js を使用して接続とデータの照会を行うには、[Node.js を使った接続とデータの照会](sql-database-connect-query-nodejs.md)に関するページを参照してください。
- Java を使用して接続とデータの照会を行うには、[Java を使った接続とデータの照会](sql-database-connect-query-java.md)に関するページを参照してください。
- Python を使用して接続とデータの照会を行うには、[Python を使った接続とデータの照会](sql-database-connect-query-python.md)に関するページを参照してください。
- Ruby を使用して接続とデータの照会を行うには、[Ruby を使った接続とデータの照会](sql-database-connect-query-ruby.md)に関するページを参照してください。

