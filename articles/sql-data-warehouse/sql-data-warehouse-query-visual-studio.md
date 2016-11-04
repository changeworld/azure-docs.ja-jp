---
title: Azure SQL Data Warehouse に対するクエリ (Visual Studio) | Microsoft Docs
description: Visual Studio で SQL Data Warehouse に対してクエリを実行します。
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/16/2016
ms.author: sonyama;barbkess

---
# Azure SQL Data Warehouse に対するクエリ (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> 
> 

Visual Studio を使用して、わずか数分で Azure SQL Data Warehouse に対するクエリを実行します。この方法では、Visual Studio の SQL Server Data Tools (SSDT) 拡張機能を使います。

## 前提条件
このチュートリアルを使用するには、次のものが必要です。

* 既存の SQL Data Warehouse。その作成方法については、[SQL Data Warehouse の作成][SQL Data Warehouse の作成]に関するページを参照してください。
* Visual Studio 用の SSDT。Visual Studio をお持ちの方は既に SSDT を所有していると思われます。インストールの手順とオプションの詳細については、[Visual Studio と SSDT のインストール][Visual Studio と SSDT のインストール]に関するページを参照してください。
* 完全修飾 SQL サーバー名。これを特定するには、[SQL Data Warehouse への接続][SQL Data Warehouse への接続]に関するページを参照してください。

## 1\.SQL Data Warehouse への接続
1. Visual Studio 2013 または 2015 を開きます。
2. SQL Server オブジェクト エクスプローラーを開きます。これを行うには、**[表示]**、**[SQL Server オブジェクト エクスプローラー]** の順に選択します。
   
    ![SQL Server Object Explorer][1]
3. **[SQL Server の追加]** アイコンをクリックします。
   
    ![Add SQL Server][2]
4. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![サーバーへの接続][3]
   
   * **[サーバー名]**: 前の手順で特定した**サーバー名**を入力します。
   * **[認証]**: **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]**: 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[接続]** をクリックします。
5. 確認のために、Azure SQL Server を展開します。サーバーに関連付けられているデータベースが表示されます。AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW][4]

## 2\.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。新しいクエリ ウィンドウが開きます。
   
    ![新しいクエリ][5]
3. 次の TSQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. クエリを実行します。そのためには、緑色の矢印をクリックするか、`CTRL`+`SHIFT`+`E` のショートカット キーを使用します。
   
    ![Run query][6]
5. クエリ結果を確認します。この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results][7]

## 次のステップ
これで接続してクエリを実行することができます。[Power BI でデータを視覚化][Power BI でデータを視覚化]してみてください。

Azure Active Directory 認証を使用するために環境を構成する方法については、[SQL Data Warehouse の認証][SQL Data Warehouse の認証]に関するページを参照してください。

<!--Arcticles-->
[SQL Data Warehouse への接続]: sql-data-warehouse-connect-overview.md
[SQL Data Warehouse の作成]: sql-data-warehouse-get-started-provision.md
[Visual Studio と SSDT のインストール]: sql-data-warehouse-install-visual-studio.md
[SQL Data Warehouse の認証]: sql-data-warehouse-authentication.md
[Power BI でデータを視覚化]: sql-data-warehouse-get-started-visualize-with-power-bi.md

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

<!---HONumber=AcomDC_0622_2016-->