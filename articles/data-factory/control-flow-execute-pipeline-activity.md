---
title: パイプラインの実行アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Execute Pipeline アクティビティを使用して、Azure Data Factory または Synapse Analytics の 1 つのパイプラインから、別のパイプラインを呼び出す方法について説明します。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 047548a39c16c5f6b6ee3f7d359a8664c87e7062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566598"
---
# <a name="execute-pipeline-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory および Synapse Analytics の Execute Pipeline アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Execute Pipeline アクティビティを使用すると、Data Factory または Synapse の 1 つのパイプラインから別のパイプラインを呼び出すことができます。

## <a name="syntax"></a>構文

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | パイプラインの実行アクティビティの名前。 | String | はい
type | **ExecutePipeline** に設定する必要があります。 | String | はい
pipeline | このパイプラインが呼び出す依存パイプラインへのパイプライン参照。 パイプライン参照オブジェクトには、**referenceName** と **type** の 2 つのプロパティがあります。 ReferenceName プロパティは、参照パイプラインの名前を指定します。 type プロパティを PipelineReference に設定する必要があります。 | PipelineReference | はい
parameters | 呼び出されたパイプラインに渡されるパラメーター | パラメーター名を引数値にマップする JSON オブジェクト | いいえ
waitOnCompletion | 依存パイプラインの実行が終了するまでアクティビティの実行を待機するかどうかを定義します。 既定値は true です。 | ブール型 | いいえ

## <a name="sample"></a>サンプル
このシナリオでは、次の 2 つのパイプラインがあります。

- **マスター パイプライン** - このパイプラインには、呼び出されたパイプラインを呼び出す 1 つのパイプラインの実行アクティビティが含まれます。 マスター パイプラインは、`masterSourceBlobContainer` と `masterSinkBlobContainer` の 2 つのパラメーターを使用します。
- **呼び出されたパイプライン** - このパイプラインには、Azure Blob ソースからデータを Azure Blob シンクにコピーする 1 つのコピー アクティビティが含まれます。 呼び出されたパイプラインは、`sourceBlobContainer` と `sinkBlobContainer` の 2 つのパラメーターを使用します。

### <a name="master-pipeline-definition"></a>マスター パイプラインの定義

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>呼び出されたパイプラインの定義

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**リンクされたサービス**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**ソース データセット**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**シンク データセット**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>パイプラインの実行

この例でマスター パイプラインを実行する場合、masterSourceBlobContainer パラメーターと masterSinkBlobContainer パラメーターに対して次の値が渡されます。 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

マスター パイプラインは、次の例で示すように、呼び出されたパイプラインにこれらの値を転送します。 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>次のステップ
サポートされている他の制御フロー アクティビティを参照してください。 

- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
