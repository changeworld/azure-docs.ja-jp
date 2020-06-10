---
title: 概要 - SQL オンデマンド (プレビュー) を使用してストレージ内のデータを照会する
description: このセクションには、Azure Synapse Analytics 内の SQL オンデマンド (プレビュー) リソースを試すために使用できるサンプル クエリが含まれています。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118262"
---
# <a name="overview-query-data-in-storage"></a>概要:ストレージ内のデータを照会する

このセクションには、Azure Synapse Analytics 内の SQL オンデマンド (プレビュー) リソースを試すために使用できるサンプル クエリが含まれています。
現在サポートされるファイル形式は、次のとおりです。  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>前提条件

クエリを発行するために必要なツール:

- 任意の SQL クライアント:
    - Azure Synapse Studio (プレビュー)
    - Azure Data Studio
    - SQL Server Management Studio

また、パラメーターは次のとおりです。

| パラメーター                                 | 説明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL オンデマンド サービス エンドポイント アドレス    | サーバー名として使用されます。                                   |
| SQL オンデマンド サービス エンドポイント リージョン     | サンプルで使用されるストレージを決定するために使用されます。 |
| エンドポイント アクセスのユーザー名とパスワード | エンドポイントへのアクセスに使用されます。                               |
| ビューの作成に使用するデータベース     | このデータベースはサンプルの開始点として使用されます。       |

## <a name="first-time-setup"></a>初回セットアップ

最初の手順として、クエリを実行する**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルでデータの読み取りに使用される外部ファイル形式が作成されます。

> [!NOTE]
> このデータベースは、実際のデータではなくメタデータを表示するためにのみ使用されます。  使用するデータベース名を書き留めておきます。後で必要になります。

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>提供されるデモ データ

デモ データには次のデータセットが含まれています。

- NYC タクシー - イエロー タクシーの移動記録 - パブリック NYC データセットの一部
  - CSV 形式
  - Parquet 形式
- 人口データ セット
  - CSV 形式
- 入れ子になった列を含むサンプルの Parquet ファイル
  - Parquet 形式
- 書籍 JSON
  - JSON 形式

| フォルダー パス                                                  | 説明                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | CSV 形式のデータの親フォルダー                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 異なる CSV 形式の人口データ ファイルを含むフォルダー。 |
| /csv/taxi/                                                   | CSV 形式の NYC パブリック データ ファイルを含むフォルダー              |
| /parquet/                                                    | Parquet 形式のデータの親フォルダー                     |
| /parquet/taxi                                                | Parquet 形式の NYC パブリック データ ファイル。Hive または Hadoop パーティション構成を使用して年別および月別に分割されています。 |
| /parquet/nested/                                             | 入れ子になった列を含むサンプルの Parquet ファイル                     |
| /json/                                                       | JSON 形式のデータの親フォルダー                        |
| /json/books/                                                 | 書籍データを含む JSON ファイル                                   |

### <a name="sample-query"></a>サンプル クエリ

検証の最後の手順は、次のクエリを実行することです。

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

上記のクエリでは、次の数値が返されます: **8945574**。

## <a name="next-steps"></a>次のステップ

これで、操作方法に関する次の記事に進む準備ができました。

- [単一の CSV ファイルに対するクエリを実行する](query-single-csv-file.md)

- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)

- [特定のファイルに対してクエリを実行する](query-specific-files.md)

- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)

- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)

- [JSON ファイルに対するクエリを実行する](query-json-files.md)

- [ビューの作成と使用](create-use-views.md)
