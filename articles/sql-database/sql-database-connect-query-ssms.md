---
title: 'SSMS: データに接続してクエリを実行する'
description: SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。
keywords: SQL データベースへの接続、SQL Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299296"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>クイック スタート:SQL Server Management Studio を使用して Azure SQL データベースに接続しクエリを実行する

このクイックスタートでは、SQL Server Management Studio (SSMS) を使用して Azure SQL データベースに接続し、クエリをいくつか実行する方法を学習します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、次のアイテムが必要です。

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/)。
- AdventureWorksLT サンプル データベース。 AdventureWorksLT データベースの作業コピーが必要な場合は、[Azure SQL データベースの作成](sql-database-single-database-get-started.md)のクイックスタートを完了して作成します。
    - この記事のスクリプトは、AdventureWorksLT データベースを使用するように記述されています。 マネージド インスタンスを使用している場合は、AdventureWorks データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。

SSMS をインストールせずにいくつかのアドホック クエリを実行するだけの場合は、「[クイックスタート:Azure portal のクエリ エディターを使用して SQL データベースに照会する](sql-database-connect-query-portal.md)」を参照してください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

自分のデータベースに接続するために必要な接続情報を取得します。 このクイックスタートを完了するには、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. クエリを実行する **[SQL データベース]** または **[SQL マネージド インスタンス]** に移動します。

3. **[概要]** ページで、完全修飾サーバー名をコピーします。 これは、単一データベースの場合は **[サーバー名]** の横にあります。またはマネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名です。 完全修飾名は、実際のサーバー名が含まれている点を除き、*servername.database.windows.net* のようになります。

## <a name="connect-to-your-database"></a>データベースに接続する

SSMS で、Azure SQL Database サーバーに接続します。

> [!IMPORTANT]
> Azure SQL Database サーバーは、ポート 1433 でリッスンします。 企業のファイアウォールの外側から SQL Database サーバーに接続するには、そのファイアウォールでこのポートが開かれている必要があります。

1. SSMS を開きます。

2. **[サーバーに接続]** ダイアログ ボックスが表示されます。 次の情報を入力します。

   | 設定      | 推奨値    | 説明 |
   | ------------ | ------------------ | ----------- |
   | **サーバーの種類** | データベース エンジン | 必須値。 |
   | **サーバー名** | 完全修飾サーバー名 | **servername.database.windows.net** のようなものです。 |
   | **認証** | SQL Server 認証 | このチュートリアルでは、SQL 認証を使用します。 |
   | **Login** | サーバー管理者アカウントのユーザー ID | サーバーを作成するために使用するサーバー管理者アカウントのユーザー ID。 |
   | **パスワード** | サーバー管理者アカウントのパスワード | サーバーを作成するために使用するサーバー管理者アカウントのパスワード。 |
   ||||

   ![[サーバーに接続]](./media/sql-database-connect-query-ssms/connect.png)  

3. **[サーバーに接続]** ダイアログ ボックスの **[オプション]** を選択します。 **[データベースへの接続]** ドロップダウン メニューで、**mySampleDatabase** を選択します。 「[前提条件](#prerequisites)」セクションのクイックスタートを完了すると、mySampleDatabase という名前の AdventureWorksLT データベースが作成されます。 AdventureWorks データベースの作業コピーの名前が mySampleDatabase と異なっている場合は、代わりにそれを選択します。

   ![サーバー上のデータベースに接続](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **[接続]** を選択します。 オブジェクト エクスプローラー ウィンドウが開きます。

5. データベースのオブジェクトを表示するには、 **[データベース]** を展開してから、自分のデータベース ノードを展開します。

   ![mySampleDatabase オブジェクト](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>クエリ データ

次の [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL のコードを実行して、カテゴリごとに上位 20 個の製品を照会します。

1. オブジェクト エクスプローラーで **mySampleDatabase** を右クリックし、 **[新しいクエリ]** を選択します。 データベースに接続された新しいクエリ ウィンドウが開きます。

2. クエリ ウィンドウに、次の SQL クエリを貼り付けます。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. ツールバーで、 **[実行]** を選択してクエリを実行し、`Product` テーブルおよび `ProductCategory` テーブルからデータを取得します。

    ![Product と ProductCategory のテーブルからデータを取得するためのクエリ](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>データの挿入

次の [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL のコードを実行して、`SalesLT.Product` テーブルに新しい製品を作成します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. **[実行]** を選択して、新しい行を `Product` テーブルに挿入します。 **メッセージ** ペインに、 **(1 行処理されました)** と表示されます。

#### <a name="view-the-result"></a>結果を表示する

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. **[実行]** を選択します。 次の結果が表示されます。

   ![Product テーブルに対するクエリの結果](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>データの更新

次の [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL コードを実行して、新しい製品を変更します。

1. 上記のクエリを、前に作成した新しいレコードを返す次のクエリに置き換えます。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **[実行]** を選択して、`Product` テーブルの指定した行を更新します。 **メッセージ** ペインに、 **(1 行処理されました)** と表示されます。

### <a name="delete-data"></a>データの削除

次の [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL コードを実行して、新しい製品を削除します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **[実行]** を選択して、`Product` テーブルの指定した行を削除します。 **メッセージ** ペインに、 **(1 行処理されました)** と表示されます。

## <a name="next-steps"></a>次のステップ

- SSMS については、[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) のページを参照してください。
- Azure Portal を使用して接続とクエリを実行するには、[Azure Portal の SQL クエリ エディターを使用した接続とクエリ実行](sql-database-connect-query-portal.md)に関するページをご覧ください。
- Visual Studio Code を使用して接続とクエリを実行するには、[Visual Studio Code を使用した接続とクエリ実行](sql-database-connect-query-vscode.md)に関するページを参照してください。
- .NET を使用して接続とクエリを実行するには、[.NET を使用した接続とクエリ実行](sql-database-connect-query-dotnet.md)に関するページを参照してください。
- PHP を使用して接続とクエリを実行するには、[PHP を使用した接続とクエリ実行](sql-database-connect-query-php.md)に関するページを参照してください。
- Node.js を使用して接続とクエリを実行するには、[Node.js を使用した接続とクエリ実行](sql-database-connect-query-nodejs.md)に関するページを参照してください。
- Java を使用して接続とクエリを実行するには、[Java を使用した接続とクエリ実行](sql-database-connect-query-java.md)に関するページを参照してください。
- Python を使用して接続とクエリを実行するには、[Python を使用した接続とクエリ実行](sql-database-connect-query-python.md)に関するページを参照してください。
- Ruby を使用して接続とクエリを実行するには、[Ruby を使用した接続とクエリ実行](sql-database-connect-query-ruby.md)に関するページを参照してください。
