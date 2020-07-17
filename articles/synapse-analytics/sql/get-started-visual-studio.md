---
title: Visual Studio と SSDT を使用して Synapse SQL に接続してクエリを実行する
description: Visual Studio と Azure Synapse Analytics を使用して、SQL プールに対してクエリを実行します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426627"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Visual Studio と SSDT を使用して Synapse SQL に接続する
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Visual Studio と Azure Synapse Analytics を使用して、SQL プールに対してクエリを実行します。 この方法では、Visual Studio 2019 の SQL Server Data Tools (SSDT) 拡張機能が使用されます。 

### <a name="supported-tools-for-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) でサポートされるツール

Visual Studio は現在、SQL オンデマンド (プレビュー) ではサポートされていません。 ただし、Azure Data Studio (プレビュー) は完全にサポートされているツールです。 SQL Server Management Studio はバージョン 18.4 から部分的にサポートされており、接続やクエリなどの機能に制限があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを使用するには、次のコンポーネントが必要です。

* 既存の SQL プール。 ない場合は、「[SQL プールを作成する](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」を参照して、この前提条件を完了してください。
* Visual Studio 用の SSDT。 Visual Studio をお持ちの方は既にこのコンポーネントを所有していると思われます。 インストールの手順とオプションの詳細については、 [Visual Studio と SSDT のインストール](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。
* 完全修飾 SQL サーバー名。 調べる方法については、「[SQL プールに接続する](connect-overview.md)」を参照してください。

## <a name="1-connect-to-sql-pool"></a>1.SQL プールに接続する
1. Visual Studio 2019 を開きます。
2. SQL Server オブジェクト エクスプローラーを開きます。 これを行うには、 **[表示]**  >  **[SQL Server オブジェクト エクスプローラー]** の順に選択します。
   
    ![[SQL Server オブジェクト エクスプローラー]](./media/get-started-visual-studio/open-ssdt.png)
3. **[SQL Server の追加]** アイコンをクリックします。
   
    ![[SQL Server の追加]](./media/get-started-visual-studio/add-server.png)
4. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![[サーバーへの接続]](./media/get-started-visual-studio/connection-dialog.png)
   
   * **[サーバー名]** : 前の手順で特定した**サーバー名**を入力します。
   * **認証**: **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[Connect]** をクリックします。
5. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述します。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
   
    ![[新しいクエリ]](./media/get-started-visual-studio/new-query2.png)
3. 次の T-SQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. クエリを実行します。 そのためには、緑色の矢印をクリックするか、 `CTRL`+`SHIFT`+`E`のショートカット キーを使用します。
   
    ![Run query](./media/get-started-visual-studio/run-query.png)
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>次のステップ
これで接続してクエリを実行することができます。[Power BI でデータを視覚化](get-started-power-bi-professional.md)してみてください。
Azure Active Directory 認証を使用するために環境を構成する方法については、[SQL プールの認証](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。
 