---
title: Azure Data Factory でのルックアップ アクティビティ | Microsoft Docs
description: ルックアップ アクティビティを使用して外部ソースから値を検索する方法を説明します。 この出力は、後続のアクティビティによってさらに参照できます。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055650"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory でのルックアップ アクティビティ

ルックアップ アクティビティを使用して、ADF でサポートされるいずれかのデータ ソースからデータセットを取得することができます。  これは、次のシナリオで使用できます。
- オブジェクト名をハード コーディングする代わりに、後続のアクティビティで操作するオブジェクト (ファイル、テーブルなど) を動的に決定する

ルックアップ アクティビティでは、構成ファイルの内容、構成テーブル、あるいはクエリまたはストアド プロシージャの実行結果を読み取り、返すことができます。  ルックアップ アクティビティからの出力は、シングルトン値である場合、後続のコピーや変換アクティビティで使用することができ、属性の配列である場合は ForEach アクティビティで使用できます。

## <a name="supported-capabilities"></a>サポートされる機能

ルックアップでは次のデータ ソースがサポートされています。 ルックアップ アクティビティによって返すことができる最大行数は **5000** で、サイズは最大 **2 MB** です。 現時点では、タイムアウト前のルックアップ アクティビティの最長期間は 1 時間です。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>構文

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>型のプロパティ
Name | 説明 | type | 必須
---- | ----------- | ---- | --------
dataset | ルックアップ用のデータセット参照を提供します。 対応する各コネクタの記事の "データセットのプロパティ" セクションから詳細を取得します。 | キーと値のペア | [はい]
source | データセット固有のソース プロパティを含みます (コピー アクティビティ ソースと同じ)。 対応する各コネクタの記事の「コピー アクティビティのプロパティ」セクションから詳細を取得します。 | キーと値のペア | [はい]
firstRowOnly | 最初の行のみまたはすべての行のどちらを返すかを示します。 | ブール | いいえ。 既定値は `true` です。

**以下の点に注意してください。**

1. ByteArray 型のソース列はサポートされていません。
2. データセット定義で構造体はサポートされていません。 特にテキスト形式のファイルの場合は、ヘッダー行を使用して列名を指定できます。
3. ルックアップ ソースが JSON ファイルである場合、JSON オブジェクトを整形するための `jsonPathDefinition` 設定はサポートされません。オブジェクト全体が取得されます。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>後続のアクティビティでルックアップ アクティビティの結果を使用する

ルックアップ結果は、アクティビティ実行結果の `output` セクションに返されます。

* **`firstRowOnly` が `true` (既定値) に設定されているときは**、出力形式は次のコードに示すとおりです。 ルックアップ結果は固定の `firstRow` キーの下にあります。 後続のアクティビティで結果を使用するには、パターン `@{activity('MyLookupActivity').output.firstRow.TableName}` を使用します。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly` が `false` に設定されているときは**、出力形式は次のコードに示すとおりです。 `count` フィールドは返されたレコードの数を示し、詳細な値は固定の `value` 配列の下に表示されます。 このような場合は通常、ルックアップ アクティビティの後に [Foreach アクティビティ](control-flow-for-each-activity.md)が続きます。 `value` 配列は、パターン `@activity('MyLookupActivity').output.value` 使用して ForEach アクティビティの `items` フィールドに渡すことができます。 `value` 配列の要素にアクセスするには、構文 `@{activity('lookupActivity').output.value[zero based index].propertyname}` を使用します。 たとえば次のようになります。`@{activity('lookupActivity').output.value[0].tablename}`

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>例
この例では、コピー アクティビティで、Azure SQL Database インスタンス内の SQL テーブルから Azure Blob Storage にデータをコピーします。 SQL テーブルの名前は、Blob Storage 内の JSON ファイルに格納されます。 ルックアップ アクティビティは、実行時にテーブル名を検索します。 このアプローチでは、パイプラインやデータセットを再デプロイすることなく JSON を動的に変更できます。 

この例では、最初の行のみのルックアップを示します。 すべての行のルックアップについて、および ForEach アクティビティで結果をチェーンするには、「[Azure Data Factory を使って複数のテーブルを一括コピーする](tutorial-bulk-copy.md)」のサンプルを参照してください。

### <a name="pipeline"></a>パイプライン
このパイプラインには、*ルックアップ*と*コピー*の 2 つのアクティビティが含まれます。 

- ルックアップ アクティビティは、Azure Blob Storage 内の場所を表す LookupDataset を使用するように設定されています。 ルックアップ アクティビティは、この場所にある JSON ファイルから SQL テーブルの名前を読み取ります。 
- コピー アクティビティは、ルックアップ アクティビティの出力 (SQL テーブルの名前) を使用します。 ソース データセット (SourceDataset) 内の tableName プロパティは、ルックアップ アクティビティからの出力を使用するように設定されています。 コピー アクティビティは、SQL テーブルから SinkDataset プロパティで指定された Azure Blob Storage 内の場所にデータをコピーします。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>ルックアップ データセット
ルックアップ データセットは、AzureStorageLinkedService タイプで指定された Azure Storage のルックアップ フォルダーにある *sourcetable.json* ファイルを参照します。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>コピー アクティビティのソース データセット
ソース データセットは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 コピー アクティビティは、この SQL テーブルから、シンク データセットで指定された Azure Blob Storage 内の場所にデータをコピーします。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>コピー アクティビティのシンク データセット
コピー アクティビティは、SQL テーブルから、AzureStorageLinkedService プロパティで指定された Azure Storage 内の *csv* フォルダーにある *filebylookup.csv* ファイルにデータをコピーします。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このストレージ アカウントには、SQL テーブルの名前を含む JSON ファイルが格納されています。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
この Azure SQL Database インスタンスには、Blob Storage にコピーされるデータが格納されています。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>オブジェクトのセット

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>オブジェクトの配列

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
