---
title: Azure Data Factory で制御テーブルを使用してデータベースの一括コピーを行う | Microsoft Docs
description: Azure Data Factory で、ソース テーブルのパーティションの一覧を格納する外部制御テーブルを使用して、データベースからデータ全体を一括コピーするソリューション テンプレートの使用方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967011"
---
# <a name="bulk-copy-from-database-with-control-table"></a>データベースから制御テーブルを使用して一括コピーを行う

Oracle サーバー、Netezza サーバー、Teradata サーバー、SQL Server などのデータ ウェアハウスから Azure にデータをコピーする場合、データ ソースの複数のテーブルから膨大なデータを読み込む必要があります。 ほとんどの場合、複数のスレッドを並列して使用して単一のテーブルから行を読み込むことができるように、各テーブルでデータをさらにパーティション化する必要があります。 提示されるテンプレートは、そのようなケースに対応するように設計されています。 

データのサイズが小さい少数のテーブルからデータをコピーする場合は、パイプライン内の単一の Copy アクティビティ、または ForEach アクティビティと Copy アクティビティを実行する "コピー データ ツール" に移動するほうが効率的です。 このテンプレートでは、この単純なユース ケースを超える操作を実行できます。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートでは、宛先ストアにコピーする必要があるソース データベースのパーティションの一覧が外部制御テーブルから取得された後、ソース データベース内の各パーティションが反復処理され、データのコピー操作が実行されます。

このテンプレートには、3 つのアクティビティが含まれています。
-   外部制御テーブルからソース データベースのパーションの一覧を取得する **Lookup** アクティビティ。
-   Lookup アクティビティからパーティションの一覧を取得した後、それぞれに対して Copy アクティビティを反復処理する **ForEach** アクティビティ。
-   ソース データベース ストアから宛先ストアに各パーティションをコピーする **Copy** アクティビティ。

このテンプレートには、5 つのパラメーターが定義されています。
-   *Control_Table_Name* パラメーターは、外部制御テーブルのテーブル名です。 制御テーブルを使用して、ソース データベースのパーティションの一覧を格納します。
-   *Control_Table_Schema_PartitionID* パラメーターは、各パーティション ID を格納する外部制御テーブルの列名です。 ソース データベース内の各パーティションのパーティション ID が一意であることを確認してください。
-   *Control_Table_Schema_SourceTableName* パラメーターは、ソース データベースの各テーブルの名前を格納する外部制御テーブルの列名です。
-   *Control_Table_Schema_FilterQuery* パラメーターは、ソース データベースの各パーティションからデータを取得するためのフィルター クエリを格納する外部制御テーブルの列名です。 たとえば、データが年単位でパーティション化されている場合、各行に格納されるクエリは、'select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''' のようになる可能性があります。
-   *Data_Destination_Folder_Path* パラメーターは、宛先ストア内のデータのコピー先であるフォルダーのパスです。  このパラメーターは、選択した宛先がファイル ベースのストレージ ストアである場合のみに表示されます。  宛先ストアとして SQL Data Warehouse を選択した場合、ここに入力する必要があるパラメーターはありません。 ただし、SQL Data Warehouse 内のテーブル名とスキーマは、ソース データベース内のものと同じである必要があります。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. SQL サーバーまたは SQL Azure 上に、一括コピーするためのソース データベースのパーティションの一覧を格納する制御テーブルを作成します。  次の例から、ソース データベースには 5 つのパーティションがあることがわかります。3 つのパーティションは *datasource_table* テーブル用であり、2 つのパーティションは別の *project_table* テーブル用です。 *LastModifytime* 列を使用して、ソース データベースの *datasource_table* テーブルのデータがパーティション化されます。 最初のパーティションを読み取るために使用されるクエリは 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''' です。  他のパーティションからデータを読み取るための類似するクエリも確認できます。 

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **[データベースの一括コピー]** テンプレートに移動し、外部制御テーブルへの**新しい接続**を作成します。  この接続は、手順 1 で制御テーブルを作成したデータベースに接続します。

    ![制御テーブルへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. データのコピー元となるソース データベースへの**新しい接続**を作成します。

     ![ソース データベースへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. データのコピー先となる宛先データ ストアへの**新しい接続**を作成します。

    ![宛先ストアへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **[このテンプレートを使用]** をクリックします。

    ![このテンプレートを使用](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 次の例に示すように、使用可能なパイプラインがパネルに表示されます。

    ![パイプラインのレビュー](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **[デバッグ]** をクリックし、パラメーターを入力し、**[完了]** をクリックします。

    ![[デバッグ] のクリック](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 次の例に示すように、使用可能な結果がパネルに表示されます。

    ![結果のレビュー](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (省略可能) データの宛先として SQL Data Warehouse を選択する場合は、ステージングとして Azure BLOB ストレージの接続も入力する必要があります。これは SQL Data Warehouse の Polybase によって要求されています。  BLOB ストレージ内のコンテナーが既に作成されていることを確認してください。  
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)