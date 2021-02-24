---
title: チュートリアル:サーバーレス SQL プールを使用してデータの分析を開始する
description: このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209408"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用してデータを分析する

このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Blob Storage の NYC タクシー データを分析する

1. **[リンク済み]** の下の **[データ]** ハブで、 **[Azure Blob Storage]** を右クリックし、 **[Sample Datasets]\(サンプル データセット\) > [nyc_tlc_yellow] > [New SQL Script]\(新しい SQL スクリプト\)** の順に選択して、 **[SELECT TOP 100 rows]\(上位 100 行の選択\)** を選択します。
1. これにより、次のコードを含む新しい SQL スクリプトが作成されます。

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. **[実行]**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Spark データベース内の NYC タクシー データを分析する

Spark データベース内のテーブルは自動的に表示され、サーバーレス SQL プールによるクエリを実行できます。

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します。
1. **[接続先]** を **組み込み** のサーバーレス SQL プールに設定します。
1. スクリプトに次のテキストを貼り付けて、スクリプトを実行します。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > サーバーレス SQL プールを使用するクエリを初めて実行するときは、クエリの実行に必要な SQL リソースをサーバーレス SQL プールで収集するのに約 10 秒かかります。 それ以降のクエリの実行時間は大幅に短縮されます。
  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ストレージ内のデータを分析する](get-started-analyze-storage.md)
