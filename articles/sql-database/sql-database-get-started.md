---
title: "Azure Portal: 単一の SQL データベースを作成してクエリを実行する | Microsoft Docs"
description: "Azure Portal で SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、データベースを作成する方法について説明します。 また、Azure Portal を使用して Azure SQL データベースへのクエリを実行する方法についても説明します。"
keywords: "SQL データベース チュートリアル, SQL データベースの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 248885d041378301c5202263d24fdc9609e293d6
ms.lasthandoff: 03/15/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Azure Portal で単一の Azure SQL データベースを作成してクエリを実行する

Azure SQL データベースは、Azure Portal で作成できます。 この方法では、ブラウザーベースのユーザー インターフェイスで Azure SQL データベースや関連するすべての Azure リソースを作成および構成できます。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com/)にログインします。

## <a name="create-a-sql-database"></a>SQL Database の作成

Azure SQL データベースは、定義済みのコンピューティング リソースとストレージ リソースのセットと共に論理サーバー上の Azure リソース グループ内に作成されます。 以下の手順に従い、Adventure Works LT サンプル データを含む SQL データベースを作成します。 

1. Azure Portal の左上にある **[新規]** ボタンをクリックします。

2. **[新規]** ブレードで **[Databases]** を選択し、**[Databases]** ブレードで **[SQL Database]** を選択します。

3. SQL Database フォームに必要な情報を入力します。 
   - [データベース名]: データベース名を指定します
   - [サブスクリプション]: サブスクリプションを選択します
   - [リソース グループ]: 新規または既存を選択します
   - [ソース]: **[Sample (AdventureWorksLT) (サンプル (AdventureWorksLT))]** を選択します
   - [サーバー]: 新しいサーバーを作成します (**サーバー**名はグローバルに一意であること)
   - [エラスティック プール]: このクイック スタートでは **[後で]** を選択します
   - [価格レベル]: **20 DTU** および **250** GB のストレージを選択します
   - [照合順序]: サンプル データベースをインポートする場合、この値は変更できません 
   - [ダッシュボードにピン留めする]: このチェック ボックスはオンにします

      ![データベースの作成](./media/sql-database-get-started/create-database-s1.png)

4. 入力が完了したら **[作成]** をクリックします。 プロビジョニングには数分かかります。
5. SQL データベースのデプロイが完了したら、ダッシュボードの **[SQL データベース]** を選択するか、左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで新しいデータベースをクリックします。 データベースの概要ページが開き、完全修飾サーバー名 (**mynewserver20170313.database.windows.net** など) や追加の構成オプションが表示されます。

      ![新しい SQL データベース](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

SQL Database サービスにより、外部のアプリケーションやツールからの新しいサーバーおよびデータベースへの接続を防止するファイアウォールが作成されます。 以下の手順に従い、SQL Database サーバーレベルのファイアウォール規則を作成して、自分の IP アドレスから SQL Database ファイアウォールを介して外部に接続できるようにします。 

1. データベースのツールバーで **[Set server firewall (サーバー ファイアウォールの設定)]** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

      ![サーバーのファイアウォール規則](./media/sql-database-get-started/server-firewall-rule.png) 

2. ツールバーの **[クライアント IP の追加]** をクリックし、**[保存]** をクリックします。 現在の IP アドレスに対してサーバーレベルのファイアウォール規則が作成されます。

3. **[OK]** をクリックしてから、**[X]** をクリックして [ファイアウォール設定] ページを閉じます。

これで、SQL Server Management Studio やその他の任意のツールを使用してデータベースとそのサーバーに接続できるようになりました。

## <a name="query-the-sql-database"></a>SQL データベースに対するクエリ

以下の手順に従い、Azure Portal でクエリ エディターを使用してデータベースに対するクエリを実行します。 

1. データベースの SQL Databse ページで、ツールバーの **[ツール]** をクリックします。 **クエリ エディター**のプレビュー ページが開きます。

     ![[ツール] メニュー](./media/sql-database-get-started/tools-menu.png) 

2. **[クエリ エディター (プレビュー)]** をクリックし、**[プレビューの使用条件]** チェック ボックスをオンにしてから **[OK]** をクリックします。 クエリ エディターが開きます。

3. **[ログイン]** をクリックし、メッセージが表示されたら **[SQL Server 認証]** を選択して、サーバーの管理者ログインとパスワードを入力します。
4. **[OK]** をクリックしてログインします。

5. 認証が済んだら、次に示すクエリのように、任意のクエリをクエリ ウィンドウに入力します。

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. **[実行]** をクリックし、**[結果]** ウィンドウでクエリの結果を確認します。

    ![クエリ エディターの結果](./media/sql-database-get-started/query-editor-results.png)

7. **[X]** をクリックしてクエリ エディターのページを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成したリソースは、保存して今後のチュートリアルで使用することができます。 または、以下の手順に従い、このクイック スタートで作成したすべてのリソースを Azure Portal で削除することもできます。

1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。 
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- Visual Studio を使用して接続を行うには、[Visual Studio を使用した接続とクエリ](sql-database-connect-query.md)に関するページを参照してください。
- SQL Database の技術的な概要については、[SQL Database サービスの説明](sql-database-technical-overview.md)に関するページを参照してください。

