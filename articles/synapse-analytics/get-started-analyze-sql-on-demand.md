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
ms.openlocfilehash: 492f8d42a072418586a6646a9beed2e750849b31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996988"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>サーバーレス SQL プールを使用してデータを分析する

このチュートリアルでは、サーバーレス SQL プールでデータを分析する方法について説明します。 

## <a name="the-built-in-serverless-sql-pool"></a>組み込みのサーバーレス SQL プール

サーバーレス SQL プールでは、容量を予約せずに SQL を使用できます。 サーバーレス SQL プールの課金は、クエリの実行に使用されたノードの数ではなく、クエリを実行するために処理されたデータの量に基づきます。

各ワークスペースは、**組み込み** と呼ばれる、事前構成されたサーバーレス SQL プールを備えています。 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>サーバーレス SQL プールを使用してニューヨーク市のタクシー データを分析する
 
> [!NOTE]
> [サンプル データがプライマリ ストレージ アカウントに配置されている](get-started-create-workspace.md#place-sample-data-into-the-primary-storage-account)ことを確認します

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. 新しい SQL スクリプトを作成します。
1. 以下のコードをスクリプトに貼り付けます。

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. **[実行]** をクリックします。 

データの探索は、自分のデータの基本的な特性が理解できる単純化されたシナリオにすぎません。 データの探索と分析の詳細については、こちらの[チュートリアル](sql/tutorial-data-analyst.md)を参照してください。

## <a name="create-data-exploration-database"></a>データ探索データベースを作成する

ファイルの内容は、`master` データベースを介して直接参照できます。 一部の単純なデータ探索シナリオであれば、別途データベースを作成する必要はありません。
ただしデータの探索を続けていく中で、次のようなユーティリティ オブジェクトを作成するとよい場合があります。
- ストレージ アカウントの名前付き参照を表す外部データ ソース。
- 外部データ ソースへの認証方法の指定を可能にするデータベース スコープの資格情報。
- 特定のデータ ソースまたはデータベース オブジェクトへのアクセス許可を持つデータベース ユーザー。
- クエリの中で使用できるユーティリティ ビュー、プロシージャ、関数。

1. `master` データベースを使用して、カスタム データベース オブジェクト用に別のデータベースを作成します。 カスタム データベース オブジェクトを `master` データベースに作成することはできません。

    ```sql
    CREATE DATABASE DataExplorationDB 
                    COLLATE Latin1_General_100_BIN2_UTF8
    ```

   > [!IMPORTANT]
   > `_UTF8` サフィックス付きの照合順序を使用することで、UTF-8 テキストが正しく `VARCHAR` 列に変換されます。 Parquet ファイルや Cosmos DB コンテナーからデータを読み取るクエリでは、`Latin1_General_100_BIN2_UTF8` によって最適なパフォーマンスが得られます。

2. 資格情報やデータ ソースなどのユーティリティ オブジェクトを作成できる `DataExplorationDB` に切り替えます。

    ```sql
    CREATE EXTERNAL DATA SOURCE ContosoLake
    WITH ( LOCATION = 'https://contosolake.dfs.core.windows.net')
    ```

   > [!NOTE]
   > 外部データ ソースは、資格情報なしで作成できます。 資格情報が存在しない場合は、外部データ ソースにアクセスするために呼び出し元の ID が使用されます。

3. 必要に応じて、'master' データベースを使用して、外部データにアクセスする予定のユーザーのログインを `DataExplorationDB` に作成します。

    ```sql
    CREATE LOGIN data_explorer WITH PASSWORD = 'My Very Strong Password 1234!';
    ```

    次に、そのログインに対してデータベース ユーザーを `DataExplorationDB` に作成し、`ADMINISTER DATABASE BULK OPERATIONS` アクセス許可を付与します。
    ```sql
    CREATE USER data_explorer FOR LOGIN data_explorer;
    GO
    GRANT ADMINISTER DATABASE BULK OPERATIONS TO data_explorer;
    GO
    ```

4. 相対パスとデータ ソースを使用してファイルの内容を探索します。

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK '/users/NYCTripSmall.parquet',
                DATA_SOURCE = 'ContosoLake',
                FORMAT='PARQUET'
        ) AS [result]
    ```

データ探索データベースは、ユーティリティ オブジェクトを格納できる単純なプレースホルダーにすぎません。 Synapse SQL プールでできることは他にもたくさんあります。Azure のデータ ソース上に構築されるリレーショナル レイヤー、つまり論理データ ウェアハウスを作成することもできます。 論理データ ウェアハウスの構築について詳しくは、こちらの[チュートリアル](sql/tutorial-data-analyst.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス Spark プールを使用してデータを分析する](get-started-analyze-spark.md)
