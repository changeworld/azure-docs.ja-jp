---
title: 'SSMS: Azure SQL Database に接続してデータを照会する | Microsoft Docs'
description: SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。
keywords: SQL データベースへの接続、SQL Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b3342164aec49967e819c316827dca9a65f2674f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098956"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>クイック スタート: SQL Server Management Studio を使用して Azure SQL Database に接続しクエリを実行する

[SQL Server Management Studio][ssms-install-latest-84g] (SSMS) を使用して、SQL Server から SQL Database まで、Microsoft Windows を対象としたあらゆる SQL インフラストラクチャを管理できます。 このクイック スタートでは、SSMS を使って Azure SQL データベースに接続し Transact-SQL ステートメントを実行してデータを照会、挿入、更新、削除する方法を示します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

* 構成済みのサーバーレベルのファイアウォール規則。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](sql-database-get-started-portal-firewall.md)」を参照してください。

#### <a name="install-the-latest-ssms"></a>最新の SSMS をインストールします。

開始する前に、最新の [SSMS][ssms-install-latest-84g] がインストールされていることを確認します。 

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>データベースに接続する

SMSS で、Azure SQL Database サーバーに接続します。 

> [!IMPORTANT]
> Azure SQL Database 論理サーバーは、ポート 1433 でリッスンします。 企業のファイアウォールの背後から論理サーバーに接続するには、そのファイアウォールでこのポートが開かれている必要があります。
>

1. SSMS を開きます。 **[サーバーへの接続]** ダイアログ ボックスが表示されます。

2. 次の情報を入力します。

   | Setting      | 推奨値    | 説明 | 
   | ------------ | ------------------ | ----------- | 
   | **サーバーの種類** | データベース エンジン | 必須値。 |
   | **サーバー名** | 完全修飾サーバー名 | 例: **mynewserver20170313.database.windows.net** |
   | **認証** | パブリック | このチュートリアルでは、SQL 認証を使用します。 |
   | **ログイン** | サーバー管理者アカウントのユーザー ID | サーバーを作成するために使用するサーバー管理者アカウントのユーザー ID。 |
   | **パスワード** | サーバー管理者アカウントのパスワード | サーバーを作成するために使用するサーバー管理者アカウントのパスワード。 |
   ||||

   ![[サーバーへの接続]](./media/sql-database-connect-query-ssms/connect.png)  

3. **[サーバーへの接続]** ダイアログ ボックスの **[オプション]** を選択します。 **[データベースへの接続]** ドロップダウン メニューで、**mySampleDatabase** を選択します。

   ![サーバー上のデータベースに接続](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **[接続]** を選択します。 オブジェクト エクスプローラー ウィンドウが開きます。 

5. データベースのオブジェクトを表示するには、**[データベース]** を展開して、**mySampleDatabase** を展開します。

   ![データベース オブジェクトを表示する](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>データのクエリを実行する

次の [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL のコードを使用して、カテゴリごとに上位 20 個の製品を照会します。

1. オブジェクト エクスプローラーで **mySampleDatabase** を右クリックし、**[新しいクエリ]** を選択します。 データベースに接続された空のクエリ ウィンドウが開きます。

1. クエリ ウィンドウに、この SQL クエリを貼り付けます。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. ツールバーで、**[実行する]** を選択して、`Product` および `ProductCategory` テーブルからデータを取得します。

    ![2 つのテーブルからデータを取得するクエリ](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>データを挿入する

次の [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL のコードを使用して、`SalesLT.Product` テーブルに新しい製品を作成します。

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

2. **[実行する]** を選択して、新しい行を Product テーブルに挿入します。 **メッセージ** ペインに、**(1 行処理されました)** と表示されます。

## <a name="view-the-result"></a>結果を表示する

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Use the following [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify the new product you just added.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **[実行する]** を選択して、Product テーブルの指定した行を更新します。 **メッセージ** ペインに、**(1 行処理されました)** と表示されます。

## <a name="delete-data"></a>データの削除

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL のコードを使用して、前に追加した新しい製品を削除します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **[実行する]** を選択して、Product テーブルの指定した行を削除します。 **メッセージ** ペインに、**(1 行処理されました)** と表示されます。

## <a name="next-steps"></a>次の手順

- SSMS については、[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) のページを参照してください。
- Azure Portal を使用して接続とクエリを実行するには、[Azure Portal の SQL クエリ エディターを使用した接続とクエリ実行](sql-database-connect-query-portal.md)に関するページをご覧ください。
- Visual Studio Code を使用して接続とデータの照会を行うには、[Visual Studio Code を使った接続とデータの照会](sql-database-connect-query-vscode.md)に関するページを参照してください。
- .NET を使用して接続とデータの照会を行うには、[.NET を使った接続とデータの照会](sql-database-connect-query-dotnet.md)に関するページを参照してください。
- PHP を使用して接続とデータの照会を行うには、[PHP を使った接続とデータの照会](sql-database-connect-query-php.md)に関するページを参照してください。
- Node.js を使用して接続とデータの照会を行うには、[Node.js を使った接続とデータの照会](sql-database-connect-query-nodejs.md)に関するページを参照してください。
- Java を使用して接続とデータの照会を行うには、[Java を使った接続とデータの照会](sql-database-connect-query-java.md)に関するページを参照してください。
- Python を使用して接続とデータの照会を行うには、[Python を使った接続とデータの照会](sql-database-connect-query-python.md)に関するページを参照してください。
- Ruby を使用して接続とデータの照会を行うには、[Ruby を使った接続とデータの照会](sql-database-connect-query-ruby.md)に関するページを参照してください。


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

