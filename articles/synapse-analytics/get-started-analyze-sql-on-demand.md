---
title: 'チュートリアル: サーバーレス SQL プールを使用したデータ分析の概要'
description: このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567555"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>サーバーレス SQL プールを使用してデータを分析する

このチュートリアルでは、サーバーレス SQL プールでデータを分析する方法について説明します。 

## <a name="the-built-in-serverless-sql-pool"></a>組み込みのサーバーレス SQL プール

サーバーレス SQL プールでは、容量を予約せずに SQL を使用できます。 サーバーレス SQL プールの課金は、クエリの実行に使用されたノードの数ではなく、クエリを実行するために処理されたデータの量に基づきます。

各ワークスペースは、**組み込み** と呼ばれる、事前構成されたサーバーレス SQL プールを備えています。 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>サーバーレス SQL プールを使用してニューヨーク市のタクシー データを分析する

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. 新しい SQL スクリプトを作成します。
1. 以下のコードをスクリプトに貼り付けます。

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. **[実行]** をクリックします。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス Spark プールを使用してデータを分析する](get-started-analyze-spark.md)
