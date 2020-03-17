---
title: Azure portal:クエリ エディターを使用してクエリを実行する
description: SQL クエリ エディターを使用して、Azure Portal で SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。
keywords: sql database への接続, azure portal, ポータル, クエリ エディター
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: b3ccc2a5343cf02127990dca80a1300959fa06a3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087186"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>クイック スタート:Azure portal の SQL クエリ エディターを使用した接続とデータの照会

SQL クエリ エディターは、お使いの Azure SQL Database または Azure SQL Data Warehouse で SQL クエリを実行するための簡単な方法を示した Azure portal ブラウザー ツールです。 このクイック スタートでは、クエリ エディターを使用して SQL データベースに接続し、Transact-SQL ステートメントを実行してデータのクエリ、挿入、更新、および削除を実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure SQL データベース。 以下のいずれかのクイック スタートを使用して、Azure SQL Database でデータベースを作成し、構成できます。

  || 単一データベース |
  |:--- |:--- |
  | 作成| [ポータル](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | 構成 | [サーバーレベルの IP ファイアウォール規則](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> クエリ エディターでは、通信にポート 443 と 1443 が使用されます。  これらのポートで送信 HTTPS トラフィックを有効にしていることを確認してください。 また、お使いのデータベースとデータ ウェアハウスにアクセスできるようにするには、サーバーの許可されたファイアウォール規則に、送信 IP アドレスを追加する必要があります。

## <a name="sign-in-the-azure-portal"></a>Azure portal へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="connect-using-sql-authentication"></a>SQL 認証を使用して接続する

1. Azure portal に移動して、SQL データベースに接続します。 **SQL データベース**を検索して選択します。

    ![SQL データベースの一覧に移動: Azure portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. SQL データベースを選択します。

    ![SQL データベースの選択: Azure portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. **[SQL データベース]** メニューで、 **[クエリ エディター (プレビュー)]** を選択します。

    ![クエリ エディターの検索](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. **[ログイン]** ページで、 **[SQL Server 認証]** ラベルの下に、データベースを作成するために使用するサーバー管理者アカウントの **[ログイン]** ID と **[パスワード]** を入力します。 **[OK]** をクリックします。

    ![sign in](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Azure Active Directory への接続

Azure Active Directory (Azure AD) 管理者を構成すると、単一の ID を使用して Azure portal と SQL データベースにサインインできます。 お使いの SQL Server の Azure AD 管理者を構成するには、次の手順に従います。

> [!NOTE]
> * 電子メール アカウント (たとえば、outlook.com、gmail.com、yahoo.com など) は、Azure AD 管理者としてはまだサポートされていません。 Azure AD でネイティブに作成されたユーザーか、Azure AD にフェデレーションされたユーザーのどちらかを必ず選択します。
> * Azure AD 管理者のサインインは、2 要素認証が有効になっているアカウントでは機能しません。

1. Azure portal メニュー上または **[ホーム]** ページから、 **[すべてのリソース]** を選択します。

2. お使いの SQL Server を選択します。

3. **[SQL Server]** メニューで、 **[設定]** の下の **[Active Directory 管理者]** を選択します。

4. SQL Server の **[Active Directory 管理者]** ページのツールバーから、 **[管理者の設定]** を選択し、ユーザーまたはグループを Azure AD 管理者として選択します。

    ![Active Directory を選択する](./media/sql-database-connect-query-portal/select-active-directory.png)

5. **[管理者の追加]** ページで、[検索] ボックスにユーザーまたはグループを入力して検索し、管理者として選択した後、 **[選択]** ボタンを選択します。

6. SQL Server の **[Active Directory 管理者]** ページに戻り、ツールバーの **[保存]** を選択します。

7. **[SQL Server]** メニューで、 **[SQL データベース]** を選択し、お使いの SQL データベースを選択します。

8. **[SQL データベース]** メニューで、 **[クエリ エディター (プレビュー)]** を選択します。 **[ログイン]** ページで、 **[Active Directory 認証]** ラベルの下に、サインインしたことを示すメッセージが表示されます (Azure AD 管理者の場合)。次に、 **[ *\<ユーザーまたはグループ ID>* として続行]** ボタンを選択します。

## <a name="view-data"></a>データの表示

1. 認証されたら、クエリ エディターに次の SQL を貼り付けて、カテゴリごとに上位 20 個の製品を取得します。

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. ツールバーで、 **[実行]** を選択して、 **[結果]** ウィンドウで出力を確認します。

   ![クエリ エディターの結果](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>データの挿入

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


2. **[実行]** を選択して、新しい行を `Product` テーブルに挿入します。 **メッセージ** ペインに、 **[クエリが成功しました: 影響を受ける行: 1]** と表示されます。


## <a name="update-data"></a>データの更新

次の [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを実行して、新しい製品を変更します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **[実行]** を選択して、`Product` テーブルの指定した行を更新します。 **メッセージ** ペインに、 **[クエリが成功しました: 影響を受ける行: 1]** と表示されます。

## <a name="delete-data"></a>データの削除

次の [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを実行して、新しい製品を削除します。

1. 前のクエリを次のクエリに置き換えます。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **[実行]** を選択して、`Product` テーブルの指定した行を削除します。 **メッセージ** ペインに、 **[クエリが成功しました: 影響を受ける行: 1]** と表示されます。


## <a name="query-editor-considerations"></a>クエリ エディターに関する考慮事項

クエリ エディターを操作するときは次の点に注意してください。

* クエリ エディターでは、通信にポート 443 と 1443 が使用されます。  これらのポートで送信 HTTPS トラフィックを有効にしていることを確認してください。 また、お使いのデータベースとデータ ウェアハウスにアクセスできるようにするには、サーバーの許可されたファイアウォール規則に、送信 IP アドレスを追加する必要があります。

* クエリ エディターは、SQL Database ファイアウォールにクライアント IP アドレスを追加しなくても、Private Link と連携します

* F5 キーを押してクエリ エディターのページを更新すると、操作中のクエリは失われます。

* クエリ エディターは、`master` データベースへの接続をサポートしません。

* クエリの実行には、5 分のタイムアウトがあります。

* geography データ型に関してクエリ エディターでサポートされるのは円柱投影のみです。

* データベース テーブルおよびビューでは、IntelliSense はサポートされていません。 ただし、エディターでは、既に入力されている名前のオートコンプリートをサポートしています。




## <a name="next-steps"></a>次のステップ

Azure SQL データベースでサポートされる Transact-SQL の詳細については、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。
