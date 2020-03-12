---
title: SSMS との接続
description: SQL Server Management Studio (SSMS) を使用して Azure Synapse Analytics に接続し、クエリを実行します。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2109402a874ff8c722bd05e1e5cb62b461cb2292
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198625"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して Azure Synapse Analytics に接続する
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

SQL Server Management Studio (SSMS) を使用して、Azure Synapse 内のデータ ウェアハウスに接続し、クエリを実行します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを使用するには、次のものが必要です。

* 既存の SQL プール。 作成するには、[SQL プールの作成](sql-data-warehouse-get-started-provision.md)に関する記事を参照してください。
* SQL Server Management Studio (SSMS) をインストールしている。 [SSMS をインストール](https://msdn.microsoft.com/library/hh213248.aspx)していない場合はインストールします (無料)。
* 完全修飾 SQL サーバー名。 この情報については、[SQL プールへの接続](sql-data-warehouse-connect-overview.md)に関する記事を参照してください。

## <a name="1-connect-to-your-sql-pool"></a>1.SQL プールに接続する
1. SSMS を開きます。
2. **[ファイル]**  >  **[オブジェクト エクスプローラーを接続]** を選択してオブジェクト エクスプローラーを開きます。
   
    ![[SQL Server オブジェクト エクスプローラー]](media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![[サーバーへの接続]](media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **[サーバー名]** : 前の手順で特定した**サーバー名**を入力します。
   * **[認証]** : **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[Connect]** をクリックします。
4. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW](media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
   
    ![[新しいクエリ]]( media/sql-data-warehouse-query-ssms/new-query.png)
3. 次の T-SQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. `Execute` をクリックしてクエリを実行するか、ショートカット キー `F5` を使用します。
   
    ![Run query](media/sql-data-warehouse-query-ssms/execute-query.png)
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results](media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>次のステップ
これで接続してクエリを実行することができます。[Power BI でデータを視覚化](sql-data-warehouse-get-started-visualize-with-power-bi.md )してみてください。
Azure Active Directory 認証を使用するために環境を構成する方法については、[SQL プールの認証](sql-data-warehouse-authentication.md)に関するページを参照してください。
