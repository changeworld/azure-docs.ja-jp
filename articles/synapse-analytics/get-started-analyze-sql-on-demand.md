---
title: チュートリアル:サーバーレス SQL を使用したデータ分析の概要
description: このチュートリアルでは、Spark データベースに格納されたデータを使用して、SQL オンデマンドでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 9c42d1d988bc280d5e62c24f109225d91cb446ce
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893494"
---
# <a name="analyze-data-with-sql-on-demand"></a>SQL オンデマンドを使用してデータを分析する

このチュートリアルでは、オンデマンド SQL プールを使用してサーバーレス SQL でデータを分析する方法について説明します。Spark データベースに格納されたデータを使用します。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>SQL オンデマンド データを使用して BLOB ストレージの NYC タクシー データを分析する

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
> [ストレージ内のデータを分析する](get-started-analyze-storage.md)
