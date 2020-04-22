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
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421296"
---
# <a name="overview-query-data-in-storage"></a>概要:ストレージ内のデータを照会する

このセクションには、Azure Synapse Analytics 内の SQL オンデマンド (プレビュー) リソースを試すために使用できるサンプル クエリが含まれています。
現在サポートされているファイルは次のとおりです。 
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

この記事の後半に記載されているサンプルを使用する前に、次の 2 つの手順を実行します。

- ビューのデータベースを作成する (ビューを使用する場合)
- SQL オンデマンドがストレージ内のファイルにアクセスするために使用する資格情報を作成する

### <a name="create-database"></a>データベースの作成

ビューを作成するには、データベースが必要です。 このデータベースは、このドキュメントの一部のサンプル クエリで使用します。

> [!NOTE]
> このデータベースは、実際のデータではなくメタデータを表示するためにのみ使用されます。  使用するデータベース名を書き留めておきます。後で必要になります。

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>資格情報を作成する

クエリを実行する前に、資格情報を作成する必要があります。 この資格情報は、SQL オンデマンド サービスがストレージ内のファイルにアクセスするために使用されます。

> [!NOTE]
> このセクションの方法を正常に実行するには、SAS トークンを使用する必要があります。
>
> SAS トークンの使用を開始するには、この[記事](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)に説明されている UserIdentity を削除する必要があります。
>
> SQL オンデマンドでは、既定で常に AAD パススルーが使用されます。

ストレージのアクセス制御の管理方法の詳細については、この[リンク](develop-storage-files-storage-access-control.md)を参照してください。

> [!WARNING]
> エンドポイント リージョンにあるストレージ アカウントの資格情報を作成する必要があります。 SQL オンデマンドは異なるリージョンからストレージにアクセスできますが、ストレージとエンドポイントを同じリージョンに配置するとパフォーマンスが向上します。

CSV、JSON、および Parquet コンテナーの資格情報を作成するには、次のコードを実行します。

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>検証

次の 3 つのクエリを実行して、資格情報が正しく作成されているかどうかを確認します。

> [!NOTE]
> サンプル クエリ内のすべての URI は、北ヨーロッパ Azure リージョンにあるストレージ アカウントを使用します。 適切な資格情報が作成されていることを確認してください。 以下のクエリを実行して、ストレージ アカウントが表示されることを確認します。

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

適切な資格情報が見つからない場合は、「[初回セットアップ](#first-time-setup)」を確認してください。

### <a name="sample-query"></a>サンプル クエリ

検証の最後の手順は、次のクエリを実行することです。

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

上記のクエリでは、次の数値が返されます: **8945574**。

## <a name="next-steps"></a>次のステップ

これで、操作方法に関する次の記事に進む準備ができました。

- [単一の CSV ファイルに対するクエリを実行する](query-single-csv-file.md)

- [フォルダーと複数の CSV ファイルに対するクエリを実行する](query-folders-multiple-csv-files.md)

- [特定のファイルに対するクエリを実行する](query-specific-files.md)

- [Parquet ファイルに対するクエリを実行する](query-parquet-files.md)

- [Parquet の入れ子にされた型に対するクエリを実行する](query-parquet-nested-types.md)

- [JSON ファイルに対するクエリを実行する](query-json-files.md)

- [ビューの作成と使用](create-use-views.md)
