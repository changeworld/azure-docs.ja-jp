---
title: 予測データ パイプラインを作成する
description: Azure Data Factory または Synapse Analytics で Machine Learning Studio (クラシック) のバッチ実行アクティビティを使用して、予測パイプラインを作成する方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: fea9f928a42503448c249ed04c753ab416cce35f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020143"
---
# <a name="create-a-predictive-pipeline-using-machine-learning-studio-classic-with-azure-data-factory-or-synapse-analytics"></a>Machine Learning Studio (クラシック) と Azure Data Factory または Synapse Analytics を使用して予測パイプラインを作成する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [現在のバージョン](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[ML Studio (classic)](https://azure.microsoft.com/documentation/services/machine-learning/) では、予測分析ソリューションをビルド、テスト、デプロイできます。 大まかに次の 3 つの手順で行われます。

1. **トレーニング実験を作成する**。 この手順を実行するには、ML Studio (classic) を使用します。 ML Studio (classic) は、トレーニング データを活用した予測分析モデルのトレーニングとテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
2. **トレーニング実験を予測実験に変換する**。 既存のデータでモデルがトレーニングされ、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
3. **Web サービスとしてデプロイする**。 Azure Web サービスとしてスコア付け実験を発行できます。 この Web サービスのエンドポイントを使用して、モデルにデータを送信し、モデルの予測を受信できます。

### <a name="using-machine-learning-studio-classic-with-azure-data-factory-or-synapse-analytics"></a>Machine Learning Studio (クラシック) と Azure Data Factory または Synapse Analytics の併用
Azure Data Factory または Synapse Analytics を使用すると、発行された [Machine Learning Studio (クラシック)](https://azure.microsoft.com/documentation/services/machine-learning) Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 パイプラインで **バッチ実行アクティビティ** を使用すると、Machine Learning Studio (クラシック) Web サービスを呼び出して、データの予測をバッチで行うことができます。

時間の経過と共に、Machine Learning Studio (クラシック) スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 次の手順を実行することで、パイプラインからモデルを再トレーニングできます。

1. 予測実験ではなく、トレーニング実験を Web サービスとして発行します。 前のシナリオで予測実験を Web サービスとして公開したのと同様にこの手順を ML Studio (classic) で行います。
2. ML Studio (classic) バッチ実行アクティビティを使用して、トレーニング実験用 Web サービスを呼び出します。 基本的には、ML Studio (classic) Batch Execution アクティビティを使用して、トレーニング Web サービスとスコア付け Web サービスの両方を呼び出すことができます。

再トレーニングを実行したら、**ML Studio (classic) 更新リソース アクティビティ** を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](update-machine-learning-models.md)」をご覧ください。

## <a name="ml-studio-classic-linked-service"></a>ML Studio (classic) のリンクされたサービス

**Machine Learning Studio (クラシック)** のリンクされたサービスを作成して、Machine Learning Studio (クラシック) Web サービスをリンクします。 このリンクされたサービスは、Machine Learning Studio (クラシック) のバッチ実行アクティビティと[更新リソース アクティビティ](update-machine-learning-models.md)で使用されます。

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

JSON 定義のプロパティについては、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。

Machine Learning Studio (クラシック) では、予測実験用にクラシック Web サービスと新しい Web サービスの両方がサポートされています。 お使いの Data Factory または Synapse ワークスペースから使用する適切なサービスを選択できます。 Machine Learning Studio (クラシック) のリンクされたサービスを作成するために必要な情報を取得するには、 https://services.azureml.net に移動します。ここには、(新しい) Web サービスとクラシック Web サービスがすべて表示されます。 アクセスする Web サービスをクリックし、 **[使用]** ページをクリックします。 **apiKey** プロパティの **主キー** と、**mlEndpoint** プロパティの **バッチ要求** をコピーします。

:::image type="content" source="./media/transform-data-using-machine-learning/web-services.png" alt-text="ML Studio (classic) Web サービス":::

## <a name="ml-studio-classic-batch-execution-activity"></a>ML Studio (classic) Batch Execution アクティビティ

次の JSON スニペットは、ML Studio (classic) バッチ実行アクティビティを定義します。 このアクティビティ定義には、先ほど作成した ML Studio (classic) のリンクされたサービスへの参照が含まれています。

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| プロパティ          | 説明                              | 必須 |
| :---------------- | :--------------------------------------- | :------- |
| name              | パイプラインのアクティビティの名前。     | はい      |
| description       | アクティビティの動作を説明するテキスト。  | いいえ       |
| type              | Data Lake Analytics U-SQL アクティビティの場合、アクティビティの種類は **AzureMLBatchExecution** です。 | はい      |
| linkedServiceName | ML Studio (classic) のリンクされたサービスにリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | はい      |
| webServiceInputs  | ML Studio (classic) Web サービスの入力名をマップするキーと値のペア。 キーは、公開済みの ML Studio (classic) Web サービスで定義されている入力パラメーターと一致する必要があります。 値は、Azure Storage のリンクされたサービスと、入力 BLOB の場所を指定する FilePath プロパティのペアです。 | いいえ       |
| webServiceOutputs | ML Studio (classic) Web サービスの出力名をマップするキーと値のペア。 キーは、公開済みの ML Studio (classic) Web サービスで定義されている出力パラメーターと一致する必要があります。 値は、Azure Storage のリンクされたサービスと、出力 BLOB の場所を指定する FilePath プロパティのペアです。 | いいえ       |
| globalParameters  | ML Studio (classic) バッチ実行サービス エンドポイントに渡されるキーと値のペア。 キーは、公開済みの ML Studio (classic) Web サービスで定義されている、Web サービスのパラメーターの名前と一致する必要があります。 値は、ML Studio (classic) バッチ実行要求の GlobalParameters プロパティに渡されます | いいえ       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>シナリオ 1:Azure Blob Storage のデータを参照する Web サービスの入力/出力の使用を実験する

このシナリオの Machine Learning Studio (クラシック) Web サービスでは、Azure Blob Storage 内のファイルのデータを使用して予測が作成され、Blob Storage に予測結果が保存されます。 次の JSON では、AzureMLBatchExecution アクティビティを使用するパイプラインが定義されています。 Azure Blog Storage の入出力データは、LinkedName と FilePath のペアを使用して参照されます。 サンプルのリンクされたサービスが入力と出力で異なる場合は、サービスが適切なファイルを選択して Machine Learning Studio (クラシック) Web サービスに送信できるように、入力と出力にそれぞれ異なるリンクされたサービスを使用できます。

> [!IMPORTANT]
> ML Studio (classic) の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 (「input1」、「input2」) がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、ML Studio (classic) エンドポイントで必要なマッピングを確認することができます。


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>シナリオ 2: リーダー/ライター モジュールを使用したさまざまなストレージのデータの参照を実験する
ML Studio (classic) の実験を作成するときのもう 1 つの一般的なシナリオは、データ インポート モジュールとデータ出力モジュールを使用することです。 データ インポート モジュールは実験にデータを読み込むために使用し、データ出力モジュールは実験からデータを保存するために使用します。 データ インポート モジュールとデータ出力モジュールの詳細については、MSDN ライブラリの[データのインポート](/azure/machine-learning/studio-module-reference/import-data)に関するトピックおよび[データの出力](/azure/machine-learning/studio-module-reference/export-data)に関するトピックをご覧ください。

データ インポート モジュールとデータ出力モジュールを使用するときは、これらのモジュールの各プロパティに Web サービスのパラメーターを使用することをお勧めします。 これらの Web パラメーターを使用すると、実行時に値を構成できます。 たとえば、次の Azure SQL Database を使用するデータ インポート モジュールで実験を作成できます: XXX.database.windows.net。 Web サービスをデプロイしたら、Web サービスのコンシューマーが `YYY.database.windows.net` という別の論理 SQL サーバーを指定できるようにします。 Web サービスのパラメーターを使用して、この値を構成できます。

> [!NOTE]
> Web サービスの入力と出力は、Web サービスのパラメーターとは異なるものです。 最初のシナリオでは、ML Studio (classic) Web サービスに対して入力と出力を指定する方法を説明しました。 このシナリオでは、データ インポート/データ出力モジュールのプロパティに対応する Web サービスのパラメーターを渡します。

Web サービス パラメーターを使用するシナリオを見てみましょう。 ML Studio (classic) でサポートされるいずれかのデータ ソース (例:Azure SQL Database) のデータをリーダー モジュールで読み取る ML Studio (classic) Web サービスをデプロイしました。 バッチ実行が実行された後、ライター モジュールを使用して結果が書き込まれます (Azure SQL Database)。  Web サービスの入力と出力は実験では定義されていません。 この場合は、リーダー/ライター モジュールに関連する Web サービス パラメーターを設定することをお勧めします。 この設定により、AzureMLBatchExecution アクティビティを使用するときにリーダー/ライター モジュールを構成できます。 Web サービスのパラメーターは、次に示すように、アクティビティの JSON の **globalParameters** セクションで指定します。

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。

再トレーニングを実行したら、**ML Studio (classic) 更新リソース アクティビティ** を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](update-machine-learning-models.md)」をご覧ください。

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
