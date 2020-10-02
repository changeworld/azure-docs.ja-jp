---
title: チュートリアル:SQL オンデマンドを使用したデータ分析の概要
description: このチュートリアルでは、Spark データベースに格納されたデータを使用して、SQL オンデマンドでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 93ebc1c5e89e54f4813f270b9f8b7b13f672fbe3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016114"
---
# <a name="analyze-data-with-sql-on-demand"></a>SQL オンデマンドを使用してデータを分析する

このチュートリアルでは、Spark データベースに格納されたデータを使用して、SQL オンデマンドでデータを分析する方法について説明します。 

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-sql-on-demand"></a>SQL オンデマンドを使用して BLOB ストレージの NYC タクシー データを分析する

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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL オンデマンドを使用して Spark データベース内の NYC タクシーのデータを分析する

Spark データベース内のテーブルは自動的に表示され、SQL オンデマンドによるクエリが可能です。

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します。
1. **[接続先]** を **[SQL on-demand]\(SQL オンデマンド\)** に設定します。
1. スクリプトに次のテキストを貼り付けて、スクリプトを実行します。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > SQL オンデマンドを使用するクエリを初めて実行する場合は、クエリの実行に必要な SQL リソースを SQL オンデマンドで収集するのに 10 秒ほどかかります。 それ以降のクエリの実行時間は大幅に短縮されます。
  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Spark を使用して分析する](get-started-analyze-spark.md)
