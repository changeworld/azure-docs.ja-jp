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
ms.date: 07/20/2020
ms.openlocfilehash: 4ca9ababbeb7843f1a014a4bd51a5e24a74acbae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322937"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用してデータを分析する

このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Blob Storage の NYC タクシー データを分析する

1. **[Linked]\(リンク済み\)** の下にある **[Data]\(データ\)** ハブで **[Azure Blob Storage]** を右クリックし、 **[nyc_tlc_yellow]、[SELECT TOP 100 rows]\(上位 100 行を選択する\)** の順に選択します
1. これにより、次のコードを含む新しい SQL スクリプトが作成されます。

    ```
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
1. **[接続先]** を **[serverless SQL pool]\(サーバーレス SQL プール\)** に設定します。
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
