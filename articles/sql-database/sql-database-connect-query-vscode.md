---
title: 'VS Code: Azure SQL Database に接続してデータを照会する | Microsoft Docs'
description: Visual Studio Code を使用して Azure SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。
keywords: SQL Database への接続
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: 994f7d300b1e9a76e109e73467a2e7f0d4c1584d
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652117"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>クイック スタート: Visual Studio Code を使って Azure SQL Database に接続して照会する

[Visual Studio Code](https://code.visualstudio.com/docs) は、Linux、macOS、Windows に対応するグラフィカル コード エディターです。 Microsoft SQL Server、Azure SQL Database、および SQL Data Warehouse のデータを照会するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。 このクイック スタートでは、Visual Studio Code を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを実行してデータのクエリ、挿入、更新、および削除を実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

必ず最新の [Visual Studio Code](https://code.visualstudio.com/Download) をインストールして [mssql 拡張機能](https://aka.ms/mssql-marketplace)を読み込んでおきます。 mssql 拡張機能のインストールのガイダンスについては、「[Install VS Code (VS コードのインストール)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)」と「[mssql for Visual Studio Code (Visual Studio Code 用 mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)」を参照してください。 

## <a name="configure-visual-studio-code"></a>Visual Studio Code を構成する 

### <a name="mac-os"></a>**Mac OS**
macOS では、mssql 拡張機能で使用される .Net Core の前提条件として、OpenSSL をインストールする必要があります。 使用するターミナルを開き、次のコマンドを入力して、**brew** と **OpenSSL** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

特別な構成は必要ありません。

### <a name="windows"></a>**Windows**

特別な構成は必要ありません。

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>言語モードを SQL に設定

Visual Studio Code で、言語モードを **[SQL]** に設定し、mssql コマンドと T-SQL IntelliSense を有効にします。

1. 新しい Visual Studio Code ウィンドウを開きます。 

2. **Ctrl** + **N** キーを押します。 新しいプレーンテキスト ファイルが開きます。 

3. ステータス バーの右下隅の **[プレーン テキスト]** を選択します。

4. 開かれた **[言語モードの選択]** ドロップダウン メニューで、**[SQL]** を選択します。 

## <a name="connect-to-your-database"></a>データベースに接続する

Visual Studio Code を使用して、Azure SQL Database サーバーに対する接続を確立します。

> [!IMPORTANT]
> 続行する前に、サーバーおよびサインインの情報が準備できていることを確認します。 接続プロファイル情報の入力を開始した後は、Visual Studio Code からフォーカスを移動すると、プロファイルの作成をやり直さなければならなくなります。
>

1. Visual Studio Code で、**Ctrl + Shift + P** キー (または **F1** キー) を押してコマンド パレットを開きます。

2. **[MS SQL:Connect]\(MS SQL: 接続\)** を選択して、**Enter** キーを押します。

3. **[接続プロファイルの作成]** を選択します。

4. プロンプトに従って、新しいプロファイルの接続プロパティを指定します。 それぞれの値を指定したら、**Enter** キーを押して続行します。 

   | プロパティ       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **サーバー名** | 完全修飾サーバー名 | 例: **mynewserver20170313.database.windows.net** |
   | **データベース名** | mySampleDatabase | 接続先のデータベース。 |
   | **認証** | SQL ログイン| このチュートリアルでは、SQL 認証を使用します。 |
   | **ユーザー名** | ユーザー名 | サーバーを作成するために使用するサーバー管理者アカウントのユーザー名。 |
   | **[パスワード (SQL ログイン)]** | パスワード | サーバーを作成するために使用するサーバー管理者アカウントのパスワード。 |
   | **[パスワードを保存しますか?]** | はい/いいえ | パスワードを毎回入力する手間を省くには、**[はい]** を選択します。 |
   | **[このプロファイルの名前を入力してください]** | プロファイル名 (**mySampleProfile** など) | 保存されたプロファイルによって、以降のログインでは、より速く接続できるようになります。 | 

   成功した場合、プロファイルが作成され接続されたことを示す通知が表示されます。

## <a name="query-data"></a>データのクエリを実行する

次の [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントによるクエリを実行して、カテゴリごとに上位 20 個の製品を照会します。

1. エディター ウィンドウで、次の SQL クエリを貼り付けます。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. **Ctrl** + **Shift** + **E** キーを押してクエリを実行し、`Product` および `ProductCategory` テーブルからの結果を表示します。

    ![2 つのテーブルからデータを取得するクエリ](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>データを挿入する

次の [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを実行して、`SalesLT.Product` テーブルに新しい製品を追加します。

1. 前のクエリを次のクエリに置き換えます。

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

2. **Ctrl** + **Shift** + **E** キーを押して、`Product` テーブルに新しい行を挿入します。

## <a name="update-data"></a>データの更新

次の [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを実行して、追加された製品を更新します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl** + **Shift** + **E** キーを押して、`Product` テーブルの指定した行を更新します。

## <a name="delete-data"></a>データの削除

次の [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL ステートメントを実行して、新しい製品を削除します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl** + **Shift** + **E** キーを押して、`Product` テーブルの指定した行を削除します。

## <a name="next-steps"></a>次の手順

- SQL Server Management Studio を使用して接続と照会を行うには、「[クイック スタート: SQL Server Management Studio を使用して Azure SQL Database に接続しクエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。
- Azure portal を使用して接続と照会を行うには、「[クイック スタート: Azure portal の SQL クエリ エディターを使って接続とデータの照会を行う](sql-database-connect-query-portal.md)」を参照してください。
- Visual Studio Code の使用に関するMSDN マガジンの記事については、[MSSQL 拡張機能によるデータベース IDE の作成に関するブログの投稿](https://msdn.microsoft.com/magazine/mt809115)を参照してください。
