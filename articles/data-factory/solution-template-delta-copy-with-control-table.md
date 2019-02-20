---
title: Azure Data Factory で制御テーブルを使用してデータベースの差分コピーを行う | Microsoft Docs
description: Azure Data Factory でソリューション テンプレートを使用して、データベースから新規行または更新行のみを増分コピーする方法について説明します。
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
ms.date: 12/24/2018
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966923"
---
# <a name="delta-copy-from-database-with-control-table"></a>データベースから制御テーブルを使用して差分コピーを行う

データベース内のテーブルから Azure に対して、変更点 (新規行または更新行) のみを増分読み込みするときは、高基準値を格納する外部制御テーブルを使用します。  提示されるテンプレートは、そのようなケースに対応するように設計されています。 

このテンプレートでは、新規行または更新行を識別するために、ソース データベースのスキーマにタイムスタンプ列または増分キーが含まれていることを要求します。

新規行または更新行を識別するタイムスタンプ列はソース データベースに含まれているが、差分コピーを可能にするために外部制御テーブルを作成したくない場合は、データ コピー ツールを使用してパイプラインを取得し、トリガーによってスケジュールが設定される時間を変数として使用して、ソース データベースから新規行のみを読み取ることができます。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートでは、常に古い基準値が最初に取得され、その後で現在の基準値と比較されます。 その後、2 つの基準値の比較に基づいて、ソース データベースから変更箇所のみがコピーされます。  完了すると、次回の差分データの読み込み用の新しい高基準値が外部制御テーブルに格納されます。

このテンプレートには、4 つのアクティビティが含まれています。
-   外部制御テーブルに格納されている古い高基準値を取得する **Lookup** アクティビティ。
-   ソース データベースから現在の高基準値を取得する **Lookup** アクティビティ。
-   ソース データベースから宛先ストアに変更箇所のみをコピーする **Copy** アクティビティ。 Copy アクティビティの中でソース データベースの変更を識別するために使用されるクエリは、'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark"' のようになります。
-   次回の差分コピーのために現在の高基準値を外部制御テーブルに書き込む **SqlServerStoredProcedure** アクティビティ。

このテンプレートには、5 つのパラメーターが定義されています。
-   *Data_Source_Table_Name* パラメーターは、データの読み込み元にするソース データベースのテーブル名です。
-   *Data_Source_WaterMarkColumn* パラメーターは、新規行または更新行を識別するために使用できるソース テーブル内の列の名前です。 通常、この列の型は datetimeや INT などが可能です。
-   *Data_Destination_Folder_Path* または *Data_Destination_Table_Name* パラメーターは、宛先ストア内のデータのコピー先です。
-   *Control_Table_Table_Name* パラメーターは、高基準値を格納する外部制御テーブルの名前です。
-   *Control_Table_Column_Name* パラメーターは、高基準値を格納する外部制御テーブルの列名です。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. 読み込み元のソース テーブルを調べて、新規行または更新行をスライスするために使用できる高基準値列を定義します。 通常は、型が datetime や INT などであり、新しい行が追加されるとデータが大きくなる列を使用できます。  次のサンプル ソース テーブル (テーブル名: data_source_table) では、高基準値列として *LastModifytime* 列を使用できます。

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. SQL サーバーまたは SQL Azure 上に、差分データを読み込むための高基準値を格納する制御テーブルを作成します。 次の例から、制御テーブルの名前は *watermarktable* であることがわかります。 その中の高基準値を格納する列の名前は *WatermarkValue* であり、その型は *datetime* です。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 同じ SQL サーバーまたは SQL Azure 上に、制御テーブルを作成するために使用されるストアド プロシージャを作成します。 このストアド プロシージャは、次回の差分データの読み込み用の新しい高基準値を外部制御テーブルに格納します。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. **[データベースの差分コピー]** テンプレートに移動し、データのコピー元となるソース データベースへの**新しい接続**を作成します。

    ![ソース テーブルへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. データのコピー先となる宛先データ ストアへの**新しい接続**を作成します。

    ![宛先テーブルへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 外部制御テーブルとストアド プロシージャへの**新しい接続**を作成します。  手順 2 と 3 で制御テーブルとストアド プロシージャを作成したデータベースに接続します。

    ![制御テーブル データ ストアへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **[このテンプレートを使用]** をクリックします。

     ![このテンプレートを使用](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 次の例に示すように、使用可能なパイプラインがパネルに表示されます。

     ![パイプラインのレビュー](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. ストアド プロシージャ アクティビティをクリックし、**[ストアド プロシージャ名]** を選択し、**[インポート パラメーター]** をクリックし、**[動的コンテンツの追加]** をクリックします。  

     ![ストアド プロシージャ アクティビティの設定](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** という内容を書き込んで **[完了]** をクリックします。  

     ![ストアド プロシージャのパラメーターの内容の記述](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. **[デバッグ]** をクリックし、パラメーターを入力し、**[完了]** をクリックします。

    ![[デバッグ] のクリック](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 次の例に示すように、使用可能な結果がパネルに表示されます。

    ![結果のレビュー](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. ソース テーブル内に新しい行を作成できます。  新しい行を作成するサンプル SQL は次のようになります。

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. **[デバッグ]** をクリックしてパイプラインを再度実行し、パラメーターを入力し、**[完了]** をクリックします。

    ![[デバッグ] のクリック](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. 宛先に新しい行のみがコピーされたことがわかります。

15. (省略可能) データの宛先として SQL Data Warehouse を選択する場合は、ステージングとして Azure BLOB ストレージの接続も入力する必要があります。これは SQL Data Warehouse の Polybase によって要求されています。  BLOB ストレージ内のコンテナーが既に作成されていることを確認してください。  
    
    ![PolyBase の構成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)