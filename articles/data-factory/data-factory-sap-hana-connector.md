---
title: "Azure Data Factory を使用した SAP HANA からのデータ移動 | Microsoft Docs"
description: "Azure Data Factory を使用して SAP HANA からデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 415e91ca2f1e47094fc8b64569b5cc29a706999a
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory を使用した SAP HANA からのデータ移動
この記事では、Azure Data Factory パイプラインのコピー アクティビティを使用して、SAP HANA と他のデータ ストアとの間でデータを移動する方法について説明します。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。 Data Factory が現在サポートしているのは、SAP HANA から他のデータ ストアへの移動だけで、他のデータ ストアから SAP HANA へのデータの移動はサポートしていません。

## <a name="supported-versions-and-installation"></a>サポートされているバージョンとインストール
このコネクタは、任意のバージョンの SAP HANA データベースのほか、 SQL クエリを使用した、HANA 情報モデル (分析ビュー、計算ビューなど) および行/列のテーブルからのデータ コピーをサポートしています。

SAP HANA への接続を有効にするには、次のコンポーネントをインストールします。
- **Data Management Gateway**: Data Factory サービスは、Data Management Gateway と呼ばれるコンポーネントを使用した、オンプレミスのデータ ストア (SAP HANA を含む) への接続をサポートしています。 Data Management Gateway の詳細およびゲートウェイの設定手順については、[オンプレミスのデータ ストアとクラウド データ ストアの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。 SAP HANA が Azure IaaS 仮想マシン (VM) でホストされている場合でも、ゲートウェイは必要です。 ゲートウェイはデータ ストアと同じ VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。
- **SAP HANA ODBC ドライバー**: ゲートウェイ コンピューターにインストールします。 SAP HANA ODBC ドライバーは [SAP ソフトウェアのダウンロード センター](https://support.sap.com/swdc)からダウンロードできます。 "**SAP HANA CLIENT for Windows**" というキーワードで検索してください。 

## <a name="supported-sinks"></a>サポート対象シンク
コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 SAP HANA のデータを、サポートされる任意のシンク データ ストアに移動できます。 

## <a name="copy-data-wizard"></a>データのコピー ウィザード
SAP HANA から、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 このサンプルは、オンプレミスの SAP HANA から Azure Blob Storage にデータをコピーする方法を示します。 Azure Data Factory のコピー アクティビティを使用して、**こちら**に記載されているシンクのいずれかにデータを[直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats)コピーすることもできます。  

> [!IMPORTANT]
> このサンプルでは、JSON のスニペットを使用します。 データ ファクトリを作成する手順は含まれてません。 手順については、記事「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

## <a name="sample-copy-data-from-sap-hana-to-azure-blob"></a>サンプル: SAP HANA から Azure BLOB にデータをコピーする
このサンプルでは、次の Data Factory のエンティティがあります。

1. [SapHana](#sap-hana-linked-service) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)型のリンクされたサービス。
3. [RelationalTable](#sap-hana-dataset) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [RelationalSource](#sap-hana-source-in-copy-activity) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルでは、SAP HANA インスタンスから Azure BLOB に 1 時間おきにデータをコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

### <a name="sap-hana-linked-service"></a>SAP HANA のリンクされたサービス
このリンクされたサービスでは、SAP HANA インスタンスをデータ ファクトリにリンクします。 type プロパティは **SapHana** に設定されます。 typeProperties セクションは、SAP HANA インスタンスの接続情報を示しています。

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このリンクされたサービスでは、Azure ストレージ アカウントをデータ ファクトリにリンクします。 type プロパティは **AzureStorage** に設定されます。 typeProperties セクションは、Azure ストレージ アカウントの接続情報を示しています。

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>SAP HANA 入力データセット

このデータセットでは、SAP HANA データセットを定義します。 Data Factory データセットの型は **RelationalTable** に設定します。 現時点では、SAP HANA データセットに対して型固有のプロパティは指定しません。 コピー アクティビティ定義のクエリでは、SAP HANA インスタンスからどのデータを読み取るかが指定されます。 

external プロパティを true に設定すると、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

頻度と間隔のプロパティでは、スケジュールを定義します。 この場合、データは 1 時間ごとに SAP HANA インスタンスから読み取られます。 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット
このデータセットでは、出力 Azure BLOB データセットを定義します。 type プロパティは AzureBlob に設定されます。 typeProperties セクションは、SAP HANA インスタンスからコピーしたデータの格納場所を示しています。 データは新しい BLOB に 1 時間おき (頻度: 時間、間隔: 1) に書き込まれます。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="pipeline-with-copy-activity"></a>コピー アクティビティのあるパイプライン

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され (SAP HANA ソースの場合)、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-hana-linked-service"></a>SAP HANA のリンクされたサービス
次の表は、SAP HANA のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
server | SAP HANA インスタンスが存在するサーバーの名前。 カスタマイズされたポートをサーバーが使用している場合は、`server:port` を指定します。 | string | はい
authenticationType | 認証の種類。 | string。 "Basic" または"Windows" | はい 
username | SAP サーバーにアクセスするユーザーの名前 | string | はい
password | ユーザーのパスワード。 | string | はい
gatewayName | Data Factory サービスが、オンプレミスの SAP HANA インスタンスへの接続に使用するゲートウェイの名前。 | string | はい
encryptedCredential | 暗号化された資格情報の文字列。 | string | なし

## <a name="sap-hana-dataset"></a>SAP HANA データセット
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型の SAP HANA データセットに対して、サポートされている型固有のプロパティはありません。 


## <a name="sap-hana-source-in-copy-activity"></a>コピー アクティビティの SAP HANA のソース
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **RelationalSource** (SAP HANA を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query | SAP HANA インスタンスからデータを読み取る SQL クエリを指定します。 | SQL クエリ。 | はい |

### <a name="type-mapping-for-sap-hana"></a>SAP HANA の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

SAP HANA からデータを移動する場合、SAP HANA 型から .NET 型に対する次のマッピングが使用されます。

SAP HANA の型 | .NET ベースの型
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
DOUBLE | Single
DECIMAL | Decimal
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | timespan
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>既知の制限事項
SAP HANA からデータをコピーする場合、既知の制限事項がいくつかあります。

- NVARCHAR 文字列は、Unicode 文字の最大文字数である 4,000 文字に切り詰められます
- SMALLDECIMAL はサポートされていません
- VARBINARY はサポートされていません
- 有効な日付は 1899/12/30 ～ 9999/12/31 です

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

