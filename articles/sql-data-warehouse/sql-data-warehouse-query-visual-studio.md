---
title: "Azure SQL Data Warehouse - VSTS への接続 |Microsoft ドキュメント"
description: "With Visual Studio には、SQL データ ウェアハウスをクエリします。"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.openlocfilehash: 1e44c6c3c47034a892753c69c5ef22a5eac18c0d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Visual Studio と SSDT を使った SQL データ ウェアハウスへの接続します。
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

ほんの数分で Azure SQL Data Warehouse のクエリに Visual Studio を使用します。 このメソッドは、Visual Studio での SQL Server Data Tools (SSDT) の拡張機能を使用します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを使用するのには、次の必要があります。

* 既存の SQL データ ウェアハウスです。 1 つを作成するを参照してください。 [SQL データ ウェアハウスを作成][Create a SQL Data Warehouse]です。
* Visual Studio 用 SSDT します。 Visual Studio の場合は、おそらくが既にあるこのです。 インストール手順とオプションでは、次を参照してください。 [Visual Studio のインストールと SSDT][Installing Visual Studio and SSDT]です。
* 完全修飾の SQL server 名。 これを参照してください[SQL データ ウェアハウスへの接続][Connect to SQL Data Warehouse]です。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.SQL データ ウェアハウスに接続します。
1. Visual Studio 2013 または 2015 を開きます。
2. SQL Server オブジェクト エクスプ ローラーを開きます。 これを行うには、次のように選択します。**ビュー** > **SQL Server オブジェクト エクスプ ローラー**です。
   
    ![SQL Server オブジェクト エクスプ ローラー][1]
3. クリックして、 **SQL Server の追加**アイコン。
   
    ![SQL Server を追加します。][2]
4. [サーバー] ウィンドウへの接続でのフィールドに入力します。
   
    ![サーバーへの接続します。][3]
   
   * **サーバー名**です。 入力、**サーバー名**以前に特定します。
   * **認証**です。 選択**SQL Server 認証**または**Active Directory 統合認証**です。
   * **ユーザー名**と**パスワード**です。 上で SQL Server 認証が選択した場合は、ユーザー名とパスワードを入力します。
   * **[接続]**をクリックします。
5. 調べるために、Azure の SQL server を展開します。 サーバーに関連付けられているデータベースを表示することができます。 AdventureWorksDW サンプル データベース内のテーブルを表示するを展開します。
   
    ![AdventureWorksDW を調査します。][4]

## <a name="2-run-a-sample-query"></a>2.サンプル クエリを実行します。
これで、データベースへの接続が確立されている、クエリを作成してみましょう。

1. SQL Server オブジェクト エクスプ ローラーでデータベースを右クリックします。
2. 選択**新しいクエリ**です。 新しいクエリ ウィンドウが開きます。
   
    ![新しいクエリ][5]
3. この TSQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. クエリを実行します。 これを行うには、緑色の矢印をクリックしてまたは次のショートカットを使用する: `CTRL` + `SHIFT` +`E`です。
   
    ![クエリを実行します。][6]
5. クエリの結果を確認します。 この例では、60398 行が、FactInternetSales テーブルにあります。
   
    ![クエリの結果][7]

## <a name="next-steps"></a>次のステップ
これで、接続して、クエリを実行することができます、 [PowerBI でデータを視覚化する][visualizing the data with PowerBI]です。

Azure Active Directory 認証用に環境を構成するのを参照してください。 [SQL データ ウェアハウスへの認証][Authenticate to SQL Data Warehouse]です。

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
