---
title: Machine Learning Studio (クラシック) モデルを更新します。
description: Machine Learning Studio (クラシック) と Azure Data Factory または Synapse Analytics を使用して予測パイプラインを作成する方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 0df985143dead0c86031e85f0a468d98e5ac8aba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077783"
---
# <a name="update--machine-learning-studio-classic-models-by-using-update-resource-activity"></a>リソースMachine Learningを使用して Studio (クラシック) モデルを更新します。

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、メインの Machine Learning Studio (クラシック) 統合に関する記事「Machine Learning [Studio (クラシック) を使用して予測パイプラインを作成する」を補完します](transform-data-using-machine-learning.md) メインの記事をまだ呼んでいない場合は、この記事を読む前にお読みください。

## <a name="overview"></a>概要
Studio (クラシック) モデルを運用化Machine Learningの一環として、モデルはトレーニングされ、保存されます。 その後、このモデルを使用して、予測 Web サービスを作成します。 これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。

Machine Learning Studio (クラシック) を使用して作成するモデルは、通常は静的ではありません。 新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。 

再トレーニングは頻繁に発生する可能性があります。 リソースBatch Execution アクティビティ更新アクティビティを使用すると、Machine Learning Studio (クラシック) モデルの再トレーニングと予測 Web サービスの更新を運用化できます。

次の図は、トレーニングと予測 Web サービスの関係を示しています。

:::image type="content" source="./media/update-machine-learning-models/web-services.png" alt-text="[Web サービス]":::

## <a name="machine-learning-studio-classic-update-resource-activity"></a>Machine Learning Studio (クラシック) のリソースの更新アクティビティ

次の JSON スニペットでは、Machine Learning Studio (クラシック) Batch Execution アクティビティ。

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| プロパティ                      | 説明                              | 必須 |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | パイプラインのアクティビティの名前。     | はい      |
| description                   | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                          | Machine Learning Studio (クラシック) 更新リソース アクティビティの場合、アクティビティの種類は **AzureMLUpdateResource です** | はい      |
| linkedServiceName             | Machine LearningupdateResourceEndpoint プロパティを含む Studio (クラシック) のリンクされたサービス。 | はい      |
| trainedModelName              | Web サービスの実験で更新されるようにトレーニング済みのモデル モジュールの名前 | はい      |
| trainedModelLinkedServiceName | 更新操作によってアップロードされる iLearner ファイルを保持する Azure Storage のリンクされたサービスの名前 | はい      |
| trainedModelFilePath          | 更新操作によってアップロードされる iLearner ファイルを表す trainedModelLinkedService 内の相対ファイル パス | はい      |

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー

モデルの再トレーニングと予測 Web サービスの更新を運用可能にするプロセス全体では、次の手順を実行する必要があります。

- **バッチ実行アクティビティ** を使用して、**トレーニング Web サービス** を呼び出します。 トレーニング Web サービスの呼び出しは、「Machine Learning Studio (クラシック) と Batch Execution アクティビティ を使用して予測パイプラインを作成する」で説明されている予測 Web サービスを呼び出[すのと同Batch Execution アクティビティ。](transform-data-using-machine-learning.md) トレーニング Web サービスの出力は、予測 Web サービスの更新に使用できる iLearner ファイルです。
- **更新リソース アクティビティ** を使用して **予測 Web サービス** の **更新リソース エンドポイント** を呼び出し、新しくトレーニングを行ったモデルでこの Web サービスを更新します。

## <a name="machine-learning-studio-classic-linked-service"></a>Machine LearningStudio (クラシック) のリンクされたサービス

上記のエンド ツー エンドのワークフローを機能するには、2 つの Machine Learning Studio (クラシック) のリンクされたサービスを作成する必要があります。

1. Machine Learning Studio (クラシック) がトレーニング Web サービスにリンクされたサービスであるこのリンクされたサービスは[、「Machine Learning Studio (クラシック)](transform-data-using-machine-learning.md)と Batch Execution アクティビティ を使用して予測パイプラインを作成する」で説明されているのと同じ方法で、Batch Execution アクティビティ によって使用されます。 相違点は、トレーニング Web サービスの出力が、予測 Web サービスを更新する更新リソース アクティビティでその後に使用される iLearner ファイルであることです。
2. 予測Machine Learningの更新リソース エンドポイントに、Machine Learning Studio (クラシック) のリンクされたサービス。 このリンクされたサービスは、上記の手順から返された iLearner ファイルを使用して予測 Web サービスを更新する更新リソース アクティビティで使用されます。

2 つ目の Machine Learning Studio (クラシック) のリンクされたサービスでは、Machine Learning Studio (クラシック) Web サービスがクラシック Web サービスまたは新しい Web サービスである場合、構成は異なります。 相違点については、以降のセクションで個別に説明します。

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web サービスが新しい Azure Resource Manager Web サービスである

Web サービスが、Azure Resource Manager エンドポイントを公開する新しい種類の Web サービスである場合は、2 番目の **既定以外** のエンドポイントを追加する必要はありません。 リンクされたサービスの **updateResourceEndpoint** の形式は次のとおりです。

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

[Machine Learning Studio (クラシック) Web サービス](https://services.azureml.net/)ポータルで Web サービスに対してクエリを実行するときに、URL 内のホルダーの値を取得できます。

新しい種類の更新リソース エンドポイントでは、サービス プリンシパルの認証が必要です。 サービス プリンシパルの認証を使用するには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、Web サービスが属するサブスクリプションまたはリソース グループの **共同作成者** または **所有者** のロールを付与します。 [サービス プリンシパルを作成し、Azure リソースを管理するためのアクセス許可を割り当てる方法](../active-directory/develop/howto-create-service-principal-portal.md)に関するページをご覧ください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

- アプリケーション ID
- アプリケーション キー
- テナント ID

リンクされたサービス定義のサンプルを次に示します。

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

次のシナリオで詳細を説明します。 この例では、パイプラインから Machine Learning Studio (クラシック) モデルを再トレーニングおよび更新します。


## <a name="sample-retraining-and-updating-an-machine-learning-studio-classic-model"></a>サンプル: Machine Learning Studio (クラシック) モデルの再トレーニングと更新

このセクションでは、**Azure Machine Learning Studio (classic) バッチ実行アクティビティ** を使用してモデルの再トレーニングを行うサンプル パイプラインを示します。 このパイプラインでは、**Azure Machine Learning Studio (classic) 更新リソース アクティビティ** を使用して、スコア付け Web サービスのモデルの更新も行います。 このセクションでは、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

### <a name="azure-blob-storage-linked-service"></a>Azure BLOB ストレージのリンクされたサービス:
Azure Storage には次のデータが格納されています。

* トレーニング データ。 Machine Learning Studio (クラシック) トレーニング Web サービスの入力データ。
* iLearner ファイル。 Machine Learning Studio (クラシック) トレーニング Web サービスからの出力。 このファイルは更新リソース アクティビティへの入力としても使用します。

リンクされたサービスのサンプルの JSON 定義を次に示します。

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-machine-learning-studio-classic-training-endpoint"></a>Machine Learning Studio (クラシック) トレーニング エンドポイントのリンクされたサービス。
次の JSON スニペットでは、Machine Learning Web サービスの既定のエンドポイントをポイントする、Machine Learning Studio (クラシック) のリンクされたサービスを定義します。

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

**Azure Machine Learning Studio (クラシック)** で、次の操作を実行して **mlEndpoint** と **apiKey** の値を取得します。

1. 左側のメニューで **[Web サービス]** をクリックします。
2. Web サービスの一覧で、 **[トレーニング Web サービス]** をクリックします。
3. **[API キー]** ボックスの隣にあるコピー ボタンをクリックします。 クリップボードにコピーされた API キーを Data Factory JSON エディターに貼り付けます。
4. **Azure Machine Learning Studio (クラシック)** で、 **[バッチ実行]** リンクをクリックします。
5. **[要求]** セクションから **要求 URI** をコピーし、それを JSON エディターに貼り付けます。

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Azure Machine Learning Studio (classic) の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットは、スコア付け Web サービスの更新可能なエンドポイントを示す Azure Machine Learning Studio (classic) のリンクされたサービスを定義します。

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>パイプライン
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 バッチ実行アクティビティはトレーニング データを入力として使用し、iLearner ファイルを出力として作成します。 更新リソース アクティビティは次に、この iLearner ファイルを取得し、予測 Web サービスの更新に使用します。

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
