---
title: Data Factory を使用して Azure Cosmos DB をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアのデータを Azure Cosmos DB にコピーしたり、Cosmos DB のデータをサポートされるシンク ストアにコピーしたりできます。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 1afd64fbd7019164f0e1f5c850f2dcd8250cdbfc
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600338"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB をコピー先またはコピー元としてデータをコピーする

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [現在のバージョン](connector-azure-cosmos-db.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (SQL API) をコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Cosmos DB のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure Cosmos DB にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

具体的には、この Azure Cosmos DB コネクタは、以下をサポートします。

- Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)
- JSON ドキュメントをそのままインポートまたはエクスポートするか、表形式のデータセット (SQL Database、CSV ファイルなど) をコピー元またはコピー先としてデータをコピーします。JSON ファイルまたは他の Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「[JSON ドキュメントのインポート/エクスポート](#importexport-json-documents)」を参照してください。

Data Factory は、Cosmos DB への書き込みの際に最適なパフォーマンスを提供するために [Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)と統合されます。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションで、Azure Cosmos DB に固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **CosmosDb** に設定する必要があります。 | [はい] |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。 次の例に示すように、接続文字列にデータベース情報を指定する必要があります。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure Cosmos DB データセットでサポートされるプロパティの一覧を示します。

Azure Cosmos DB をコピー元またはコピー先としてデータのコピーを行うには、データセットの type プロパティを **DocumentDbCollection** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**DocumentDbCollection** を設定する必要があります。 |[はい] |
| collectionName |Cosmos DB ドキュメント コレクションの名前です。 |[はい] |

**例:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Cosmos DB などのスキーマのないデータ ストアの場合、コピー アクティビティは、次のいずれかの方法でスキーマを推論します。 したがって、[JSON ドキュメントをそのままインポートまたはエクスポートする](#importexport-json-documents)場合以外は、**structure** セクションにデータの構造を指定することがベスト プラクティスです。

*. データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
*. データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory サービスはデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の行が欠落します。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Cosmos DB のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-cosmos-db-as-source"></a>ソースとしての Azure Cosmos DB

Azure Cosmos DB からデータをコピーするには、コピー アクティビティのソースの種類を **DocumentDbCollectionSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **DocumentDbCollectionSource** を設定する必要があります。 |[はい] |
| query |データを読み取る Cosmos DB クエリを指定します。<br/><br/>例: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ  <br/><br/>指定されていない場合に実行される SQL ステートメント: `select <columns defined in structure> from mycollection` |
| nestingSeparator |ドキュメントが入れ子になっていることと、結果セットの入れ子を解除する方法を示す特殊文字。<br/><br/>たとえば、Cosmos DB クエリで入れ子になった結果 `"Name": {"First": "John"}` が返された場合、nestedSeparator がドットであるとき、コピー アクティビティは、値が "John" である列名 "Name.First" として識別します。 |いいえ (既定値はドット `.`) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>シンクとしての Azure Cosmos DB

Azure Cosmos DB へデータをコピーするには、コピー アクティビティのシンクの種類を **DocumentDbCollectionSink** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **DocumentDbCollectionSink** に設定する必要があります |[はい] |
| writeBehavior |Cosmos DB にデータを書き込む方法を示します。 使用できる値は、`insert` と `upsert` です。<br/>**upsert** の動作は、同じ ID を持つドキュメントが既に存在する場合に、そのドキュメントを置き換えることです。それ以外の場合はドキュメントを挿入します。 ADF は、ドキュメントの ID が元のドキュメント (または列のマッピング) に指定されていない場合は、それを自動的に生成します。つまり、upsert を期待どおりに動作させるには、ドキュメントが "ID" を持っていることを確認する必要があります。 |いいえ (既定値は Insert) |
| writeBatchSize | Data Factory は、[Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)を使用して Cosmos DB にデータを書き込みます。 "writeBatchSize" は、ライブラリに提供するドキュメントのサイズを毎回制御します。 writeBatchSize を増やしてパフォーマンスを向上させることを試みることができます。 |いいえ (既定値は 10,000) |
| nestingSeparator |入れ子になった文書が必要であることを示すソース列名の特殊文字。 <br/><br/>たとえば、出力データセット構造内の `Name.First` は、nestedSeparator がドットの場合は、次の JSON 構造を Cosmos DB ドキュメント内に生成します。`"Name": {"First": "[value maps to this column from source]"}` |いいえ (既定値はドット `.`) |

**例:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>JSON ドキュメントのインポート/エクスポート

この Cosmos DB コネクタを使って、次の作業を簡単に実行できます。

* Azure BLOB、Azure Data Lake Store、Azure Data Factory でサポートされているその他のファイル ベースのストアなどのさまざまなソースから Cosmos DB に JSON ドキュメントをインポートする。
* JSON ドキュメントを Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートする。
* 2 つの Cosmos DB コレクション間でドキュメントをそのままコピーする。

このようなスキーマに依存しないコピーを実行するには:

* データのコピー ツールを使う場合は、**[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションをオンにします。
* アクティビティ作成を使う場合は、コピー アクティビティで、Cosmos DB データセットの "structure" (スキーマとも呼ばれます) セクションも Cosmos DB ソース/シンクの "nestingSeparator" プロパティも指定しないでください。 JSON ファイルに対するインポート/エクスポートを行うときに、対応するファイル ストア データセットに、形式の種類 "JsonFormat" を指定し、"filePattern" プロパティを構成します (詳細は「[JSON 形式](supported-file-formats-and-compression-codecs.md#json-format)」セクションを参照)。次に、"structure" (スキーマとも呼ばれます) セクションを指定せず、残りの形式設定セクションをスキップします。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
