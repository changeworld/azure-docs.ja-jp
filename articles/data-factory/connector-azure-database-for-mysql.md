---
title: "Azure Data Factory を使用して Azure Database for MySQL からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Database for MySQL からサポートされているシンク データ ストアへデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 232096acb2f79e1c1a7816e4074674b48534c440
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-azure-database-for-mysql-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Database for MySQL からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Database for MySQL からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の MySQL コネクタ](v1/data-factory-onprem-mysql-connector.md)に関する記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Database for MySQL のデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションでは、Azure Database for MySQL コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Database for MySQL のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureMySql** に設定する必要があります | あり |
| connectionString | Azure Database for MySQL インスタンスに接続するために必要な情報を指定します。 このフィールドを SecureString とマークします。 | あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=3306;Database=<database>;UID=<username>;PWD=<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Azure Database for MySQL データセットでサポートされるプロパティの一覧を示します。

Azure Database for MySQL からデータをコピーするには、データセットの type プロパティを **AzureMySqlTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **AzureMySqlTable** に設定する必要があります | あり |
| tableName | MySQL データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Database for MySQL ソースでサポートされるプロパティの一覧を示します。

### <a name="azure-database-for-mysql-as-source"></a>ソースとしての Azure Database for MySQL

Azure Database for MySQL からデータをコピーするには、コピー アクティビティのソースの種類を **AzureMySqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **AzureMySqlSource** に設定する必要があります | あり |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ型のマッピング

Azure Database for MySQL からデータをコピーするとき、MySQL のデータ型から Azure Data Factory の中間データ型への次のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure Database for MySQL データ型 | Data Factory の中間データ型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Decimal` |
| `blob` |`Byte[]` |
| `bool` |`Boolean` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。