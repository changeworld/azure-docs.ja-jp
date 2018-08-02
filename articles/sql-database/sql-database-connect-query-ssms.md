---
title: 'SSMS: Azure SQL Database に接続してデータを照会する | Microsoft Docs'
description: SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。
keywords: SQL データベースへの接続、SQL Server Management Studio
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 9177ad9cc4cbb668599d35b295ab939cea148a5a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412816"
---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: SQL Server Management Studio を使って接続とデータの照会を行う

[SQL Server Management Studio][ssms-install-latest-84g] (SSMS) は、SQL Server から SQL Database まで、Microsoft Windows を対象としたあらゆる SQL インフラストラクチャを管理するための統合環境です。 このクイック スタートでは、SSMS を使って Azure SQL データベースに接続し Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-the-latest-ssms"></a>最新の SSMS をインストールします。

開始する前に、必ず最新バージョンの [SSMS][ssms-install-latest-84g] をインストールしておいてください。 

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>データベースに接続する

SQL Server Management Studio を使用して、Azure SQL Database サーバーに対する接続を確立します。 

> [!IMPORTANT]
> Azure SQL Database 論理サーバーは、ポート 1433 でリッスンします。 企業のファイアウォール内から Azure SQL Database 論理サーバーに接続する場合、企業のファイアウォールでこのポートが開放されていないと、正しく接続することができません。
>

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

   | Setting      | 推奨値    | 説明 | 
   | ------------ | ------------------ | ----------- | 
   | **サーバーの種類** | データベース エンジン | この値は必須です。 |
   | **サーバー名** | 完全修飾サーバー名 | 名前は **mynewserver20170313.database.windows.net** のような形式で指定する必要があります。 |
   | **認証** | パブリック | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
   | **ログイン** | サーバー管理者アカウント | これは、サーバーの作成時に指定したアカウントです。 |
   | **パスワード** | サーバー管理者アカウントのパスワード | これは、サーバーの作成時に指定したパスワードです。 |
   ||||

   ![[サーバーへの接続]](./media/sql-database-connect-query-ssms/connect.png)  

3. **[サーバーへの接続]** ダイアログ ボックスの **[オプション]** をクリックします。 **[データベースへの接続]** セクションに「**mySampleDatabase**」と入力して、このデータベースに接続します。

   ![サーバー上のデータベースに接続](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **[接続]** をクリックします。 SSMS でオブジェクト エクスプローラー ウィンドウが開きます。 

   ![サーバーに接続されました](./media/sql-database-connect-query-ssms/connected.png)  

5. オブジェクト エクスプローラーで、**Databases** フォルダー、**mySampleDatabase** フォルダーの順に展開して、サンプル データベース内のオブジェクトを表示します。

## <a name="query-data"></a>データのクエリを実行する

次のコードを使用して、カテゴリ別で上位 20 の製品を照会します。ここでは [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントを使用します。

1. オブジェクト エクスプローラーで **mySampleDatabase** を右クリックし、**[新しいクエリ]** をクリックします。 データベースに接続された空のクエリ ウィンドウが開きます。
2. クエリ ウィンドウに次のクエリを入力します。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. ツール バーの **[実行]** をクリックして、Product と ProductCategory のテーブルからデータを取得します。

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>データを挿入する

次のコードを使用して、SalesLT.Product テーブルに新しい製品を挿入します。ここでは [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを使用します。

1. クエリ ウィンドウで、先ほどのクエリを次のクエリに置き換えます。

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

2. ツール バーの **[実行]** をクリックして、新しい行を Product テーブルに挿入します。

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>データの更新

次のコードを使用して、先ほど追加した新しい製品を更新します。ここでは [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを使用します。

1. クエリ ウィンドウで、先ほどのクエリを次のクエリに置き換えます。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. ツール バーの **[実行]** をクリックして、Product テーブルの指定した行を更新します。

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>データの削除

次のコードを使用して、先ほど追加した新しい製品を削除します。ここでは [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用します。

1. クエリ ウィンドウで、先ほどのクエリを次のクエリに置き換えます。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. ツール バーの **[実行]** をクリックして、Product テーブルの指定した行を削除します。

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>次の手順

- SSMS については、「[Use SQL Server Management Studio (SQL Server Management Studio の使用)](https://msdn.microsoft.com/library/ms174173.aspx)」を参照してください。
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

