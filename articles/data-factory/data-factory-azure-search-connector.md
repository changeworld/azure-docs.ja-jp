---
title: "Data Factory を使用して Search インデックスにデータをプッシュする | Microsoft Docs"
description: "Azure Data Factory を使用して Azure Search インデックスにデータをプッシュする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2
ms.lasthandoff: 12/21/2016

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Search インデックスにデータをプッシュする
この記事では、Data Factory サービスでサポートされているオンプレミスのデータ ストアから Azure Search インデックスに、コピー アクティビティを使用してデータをプッシュする方法について説明します。 サポートされているソース データ ストアについては、[サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表のソースの列を参照してください。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## <a name="enabling-connectivity"></a>接続を有効にする
Data Factory サービスからオンプレミスのデータ ストアに接続できるようにするには、オンプレミスの環境に Data Management Gateway をインストールします。 データ ストアをホストするコンピューターと同じコンピューター、またはデータ ストアとのリソースの競合を避けるために別のコンピューター上にゲートウェイをインストールできます。

Data Management Gateway では、安全かつ管理された方法でオンプレミスのデータがクラウド サービスに接続されます。 Data Management Gateway の詳細については、 [オンプレミスとクラウド間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。

## <a name="copy-data-wizard"></a>データのコピー ウィザード
サポートされている任意のソース データ ストアから Azure Search にデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 [コピー ウィザードを使用してパイプラインを作成するチュートリアル](data-factory-copy-data-wizard-tutorial.md)に関する記事で、簡単なチュートリアルをご覧いただけます。

次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、オンプレミスの SQL Server から Azure Search インデックスにデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に記載されているいずれかのオンプレミス データ ストアからデータをコピーすることができます。   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>サンプル: オンプレミスの SQL Server から Azure Search インデックスにデータをコピーする

次のサンプルは以下を示しています。

1.    [AzureSearch](#azure-search-linked-service-properties) 型のリンクされたサービス。
2.    [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)型のリンクされたサービス。
3.    [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.    [AzureSearchIndex](#azure-search-index-dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.    [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) と [AzureSearchIndexSink](#azure-search-index-sink-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、オンプレミスの SQL Server データベースから Azure Search インデックスに時系列データを 1 時間おきにコピーします。 このサンプルで使用される JSON プロパティの説明は、サンプルに続くセクションにあります。

最初の手順として、オンプレミスのコンピューターでデータ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

**Azure Search のリンクされたサービス:**

```JSON
{
    "name": "AzureSearchLinkedService",
       "properties": {
        "type": "AzureSearch",
           "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
       }
}
```

**SQL Server のリンクされているサービス**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 入力データセット**

このサンプルでは、SQL Server で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。 1 つのデータセットを使用して同じデータベース内の複数のテーブルに対してクエリを実行することはできますが、データセットの tableName typeProperty には 1 つのテーブルを使用する必要があります。

"external": "true" の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Search の出力データセット:**

このサンプルは、データを **products** という名前の Azure Search インデックスにコピーします。 Data Factory では、インデックスは作成されません。 サンプルをテストするには、この名前を持つインデックスを作成します。 入力データセットと同数の列を持つ Azure Search インデックスを作成します。 新しいエントリが、Azure Search インデックスに 1 時間おきに追加されます。

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
         "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **AzureSearchIndexSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```

クラウド データ ストアからAzure Search へデータをコピーする場合は、`executionLocation` プロパティが必要です。 コピー アクティビティの下にある `typeProperties` を例として、必要な変更を以下に示します。 「[クラウド データ ストア間でのデータのコピー](data-factory-data-movement-activities.md#global)」セクションで、サポートされている値および詳細を確認してください。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="azure-search-linked-service-properties"></a>Azure Search のリンクされたサービスのプロパティ

次の表は、Azure Search のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを **AzureSearch** に設定する必要があります。 | あり |
| URL | Azure Search サービスの URL。 | はい |
| key | Azure Search サービスの管理者キー。 | はい |

## <a name="azure-search-index-dataset-properties"></a>Azure Search インデックスのデータセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、 [データセットの作成](data-factory-create-datasets.md) に関する記事をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。 **typeProperties** セクションは、データセットの型ごとに異なります。 **AzureSearchIndex** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを **AzureSearchIndex** に設定する必要があります。| あり |
| indexName | Azure Search インデックスの名前。 Data Factory では、インデックスは作成されません。 Azure Search にこのインデックスが存在する必要があります。 | あり |


## <a name="azure-search-index-sink-properties"></a>Azure Search インデックスの Sink プロパティ
アクティビティの定義に使用できるセクションとプロパティの完全な一覧については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事をご覧ください。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。 一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **AzureSearchIndexSink** である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | ドキュメントがそのインデックスに既に存在する場合に、マージするか置換するかを指定します。 詳細については、「[WriteBehavior プロパティ](#writebehavior-property)」を参照してください。| マージ (既定値)<br/>[アップロード]| なし |
| WriteBatchSize | バッファー サイズが writeBatchSize に達したときに、Azure Search インデックスにデータをアップロードします。 詳細については、「[WriteBatchSize プロパティ](#writebatchsize-property)」を参照してください。 | 1 ～ 1,000。 既定値は 1,000 です。 | なし |

### <a name="writebehavior-property"></a>WriteBehavior プロパティ
データを書き込むときに AzureSearchSink で upsert されます。 つまり、ドキュメントを書き込むときに Azure Search インデックスにそのドキュメントのキーが既に存在する場合は、Azure Search は競合の例外をスローするのではなく、既存のドキュメントを更新します。

AzureSearchSink で提供される upsert 動作 (AzureSearch SDK の使用による) は、次の 2 とおりあります。

- **マージ**: 新しいドキュメントのすべての列を既存の列と結合します。 新しいドキュメント内に null 値を持つ列がある場合は、既存の列の値が保持されます。
- **アップロード**: 既存のドキュメントが新しいドキュメントで置き換えられます。 新しいドキュメントで指定されていない列の場合は、既存のドキュメントに null 以外の値があるかどうかに関係なく、値は null に設定されます。

既定の動作は**マージ**です。

### <a name="writebatchsize-property"></a>WriteBatchSize プロパティ
Azure Search サービスでは、バッチとしてのドキュメントの書き込みをサポートていします。 バッチには、1 ～ 1,000 のアクションを含めることができます。 1 つのアクションで、1 つのドキュメントのアップロード/マージ操作の実行を処理します。

### <a name="data-type-support"></a>データ型のサポート
次の表は、Azure Search データ型がサポートされているかどうかを示します。

| Azure Search データ型 | Azure Search のシンクでサポートされている |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| ブール | Y |
| DataTimeOffset | Y |
| 文字列配列 | N |
| GeographyPoint | N |

## <a name="copy-from-a-cloud-source"></a>クラウド ソースからコピー
クラウド データ ストアからAzure Search へデータをコピーする場合は、`executionLocation` プロパティが必要です。 コピー アクティビティの下にある `typeProperties` を例として、必要な変更を以下に示します。 「[クラウド データ ストア間でのデータのコピー](data-factory-data-movement-activities.md#global)」セクションで、サポートされている値および詳細を確認してください。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>四角形のデータセットの構造定義を指定する
データセット JSON の structure セクションは、(行と列がある) 四角形のテーブルの**省略可能**なセクションです。テーブルの列のコレクションが含まれています。 structure セクションは、型変換のために型情報を提供したり、列マッピングを実行したりするために使用します。 次のセクションでは、これらの機能の詳細について説明します。

各列には次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| name | 列の名前です。 | あり |
| type | 列のデータ型です。 型情報を指定する必要があるタイミングの詳細については、[型変換](#type-conversion-sample)のセクションを参照してください。 | いいえ |
| culture | 型を指定するときに使用される .NET ベースのカルチャ。.NET 型の `Datetime` または `Datetimeoffset` です。 既定値は `en-us` です。  | なし |
| BlobSink の format | 型を指定するときに使用される書式指定文字列。.NET 型の `Datetime` または `Datetimeoffset` です。 | なし |

次に、`userid`、`name`、および `lastlogindate` という 3 つの列があるテーブルの structure セクション JSON の例を示します。

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
"structure" 情報を含めるタイミングと、**structure** セクションに含める内容については、次のガイドラインに従ってください。

- データ自体と共にデータ スキーマと型情報を格納する**構造化データ ソースの場合** (例: SQL Server、Oracle、Azure テーブルなど)、特定のソース列をシンク内の特定の列とマップし、それらの列名が同じではない場合のみ、"structure" セクションを指定します。 詳細については、列マッピングのセクションを参照してください。

    前述のように、"structure" セクションの型情報は省略可能です。 構造化ソースの場合、型情報はデータ ストアのデータセット定義の一部として使用可能なので、"structure" セクションを含める場合に型情報を含めないでください。
- **読み取りデータ ソースのスキーマの場合 (具体的には Azure BLOB)**、データと共にスキーマや型情報を保存せずに、データを保存することができます。 このような種類のデータ ソースでは、次の 2 つの場合に "structure" を含める必要があります。
    - ソース列をシンク列にマップする場合。
    - データセットがコピー アクティビティのソースであるときに、"structure" で型情報を提供できる場合。 Data Factory では、シンクのネイティブ型に変換するためにこの型情報を使用します。 詳細については、[Azure BLOB との間のデータの移動](data-factory-azure-blob-connector.md)に関する記事を参照してください。

### <a name="supported-net-based-types"></a>サポートされる .NET ベースの型
Data Factory は、Azure BLOB などの読み取りデータ ソースのスキーマに "structure" で型情報を提供する場合、次の CLS 準拠の .NET ベースの型値をサポートしています。

- Int16
- Int32
- Int64
- Single
- Double
- Decimal
- ブール値
- String
- Datetime
- Datetimeoffset
- Timespan

Datetime と Datetimeoffset の場合、必要に応じて "culture" と "format" 文字列を指定して、カスタムの Datetime 文字列の解析に利用することができます。 型変換のサンプルについては、次のセクションを参照してください。


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>パフォーマンスとチューニング  
データ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

* [コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) を参照してください。

