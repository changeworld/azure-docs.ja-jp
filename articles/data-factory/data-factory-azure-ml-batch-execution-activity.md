---
title: "Machine Learning のアクティビティの使用 | Microsoft Docs"
description: "Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: ec522d843b2827c12ff04afac15d89d525d88676
ms.openlocfilehash: aa7b7ff406d06016aa6c4ee956dbf10a856a0e24


---
# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Azure Machine Learning アクティビティを使用して予測パイプラインを作成する

> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md) 
> * [Pig](data-factory-pig-activity.md)
> * [MapReduce](data-factory-map-reduce.md)
> * [Hadoop ストリーミング](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [ストアド プロシージャ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET カスタム](data-factory-use-custom-activities.md)
>

## <a name="introduction"></a>はじめに
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) では、予測分析ソリューションをビルド、テスト、およびデプロイできます。 大まかに次の 3 つの手順で行われます。

1. **トレーニング実験を作成する**。 この手順を実行するには、Azure ML Studio を使用します。 ML Studio は、トレーニング データを活用した予測分析モデルのトレーニングとテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
2. **トレーニング実験を予測実験に変換する**。 既存のデータでモデルがトレーニングされ、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
3. **Web サービスとしてデプロイする**。 Azure Web サービスとしてスコア付け実験を発行できます。 この Web サービスのエンドポイントを使用して、モデルにデータを送信し、モデルの予測を受信できます。  

Azure Data Factory を使用すると、公開された [Azure Machine Learning][azure-machine-learning] Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory サービスについては、[Azure Data Factory の概要](data-factory-introduction.md)と [Azure Data Factory を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline.md)に関するページを参照してください。

Azure Data Factory パイプラインで **バッチ実行アクティビティ** を使用すると、Azure ML Web サービスを呼び出して、データの予測を一括で行うことができます。 詳細については、「 [バッチ実行アクティビティを使用して、Azure ML Web サービスを呼び出す](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) 」を参照してください。

時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 次の手順を実行することで、Data Factory パイプラインから Azure ML モデルを再トレーニングできます。

1. 予測実験ではなく、トレーニング実験を Web サービスとして発行します。 前のシナリオで予測実験を Web サービスとして公開したのと同様にこの手順を Azure ML Studio で行います。
2. Azure ML バッチ実行アクティビティを使用して、トレーニング実験用 Web サービスを呼び出します。 基本的には、Azure ML バッチ実行アクティビティを使用して、トレーニング Web サービスとスコア付け Web サービスの両方を呼び出すことができます。

再トレーニングを実行したら、スコア付け Web サービス (Web サービスとして公開した予測実験) を新しくトレーニングを行ったモデルで更新する必要があります。 手順は次のようになります。

1. スコア付け Web サービスに既定以外のエンドポイントを追加します。 Web サービスの既定のエンドポイントは更新できません。そのため、Azure Portal を使用して既定以外のエンドポイントを作成する必要があります。 この概念と手順については、[エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md)に関するページを参照してください。
2. 既存のスコア付け用 Azure ML のリンクされたサービスを、既定以外のエンドポイントを使用するように更新します。 更新された Web サービスを使用するには、新しいエンドポイントの使用を開始します。
3. **Azure ML 更新リソース アクティビティ** を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

詳細については、「 [更新リソース アクティビティを使用して Azure ML モデルを更新する](#updating-azure-ml-models-using-the-update-resource-activity) 」を参照してください。

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>バッチ実行アクティビティを使用して Web サービスを呼び出す
Azure Data Factory を使用してデータの移動と処理を調整した後、Azure Machine Learning を使用してバッチを実行します。 大まかな手順を以下に示します。

1. Azure Machine Learning のリンクされたサービスを作成します。 以下のものが必要になります。

   1. **要求 URI** 。 要求 URI は、Web サービス ページで **[Batch 実行]** リンクをクリックするとわかります。
   2. **API キー** 。 API キーは、発行した Web サービスをクリックするとわかります。

      1. **AzureMLBatchExecution** アクティビティを使用します。

      ![Machine Learning Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>シナリオ: Azure Blob Storage のデータを参照する Web サービスの入力/出力を使用する
このシナリオの Azure Machine Learning Web サービスは、Azure BLOB ストレージ内のファイルのデータを使用して予測を作成し、BLOB ストレージに予測結果を保存します。 次の JSON では、AzureMLBatchExecution アクティビティを使用する Data Factory パイプラインが定義されています。 このアクティビティは、入力としてデータセット **DecisionTreeInputBlob** を使用し、出力として **DecisionTreeResultBlob** を使用します。 **DecisionTreeInputBlob** は、**webServiceInput** JSON プロパティを使用して Web サービスの入力として渡します。 **DecisionTreeResultBlob** は、**webServiceOutputs** JSON プロパティを使用して Web サービスの出力として渡します。  

> [!IMPORTANT]
> Web サービスで複数の入力を受け取る場合は、**webServiceInput** プロパティを使用せずに、**webServiceInputs** プロパティを使用します。 webServiceInputs プロパティの使用例については、「 [Web サービスで複数の入力が必要である](#web-service-requires-multiple-inputs) 」のセクションを参照してください。
>
> **webServiceInput**/**webServiceInputs** と **webServiceOutputs** の各プロパティ (**typeProperties** 内) から参照されているデータセットもアクティビティの **inputs** と **outputs** に含める必要があります。
>
> Azure ML の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure ML エンドポイントで必要なマッピングを確認することができます。
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> AzureMLBatchExecution アクティビティの入力および出力だけを、パラメーターとして Web サービスに渡すことができます。 たとえば、上の JSON スニペットでは、DecisionTreeInputBlob は AzureMLBatchExecution アクティビティへの入力であり、webServiceInput パラメーターを使用して Web サービスに入力として渡されます。   
>
>

### <a name="example"></a>例
この例では、Azure Storage を使用して、入力データと出力データの両方を保持します。

この例を実行する前に、[Data Factory を使用して最初のパイプラインを作成する][adf-build-1st-pipeline]チュートリアルを完了することをお勧めします。 この例では、Data Factory エディターを使用して Data Factory アーティファクト (リンクされたサービス、データセット、パイプライン) を作成します。   

1. **Azure Storage** 用の**リンクされたサービス**を作成します。 入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。 JSON の例を次に示します。

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. **入力**用の Azure Data Factory **データセット**を作成します。 他の Data Factory データセットとは異なり、これらのデータセットは **folderPath** 値と **fileName** 値を含む必要があります。 パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。 入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するため、いくつかのアップストリーム アクティビティを含める場合があります。 その場合は、次の例に示す **external** および **externalData** の設定を含めないと、DecisionTreeInputBlob は別のアクティビティの出力データセットになります。

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    入力 csv ファイルには列ヘッダー行がある必要があります。 **コピー アクティビティ**を使用している csv を BLOB ストレージに作成または移動する場合、シンクの **blobWriterAddHeader** プロパティを **true** に設定する必要があります。 次に例を示します。

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    csv ファイルにヘッダー行がない場合、次のエラーが表示される場合があります: **アクティビティ エラー: 文字列の読み取りエラー。予期しないトークン: StartObject。パス ''、行 1、位置 1**。
3. **出力**用の Azure Data Factory **データセット**を作成します。 この例では、パーティション分割を使用して各スライスの実行ごとに一意の出力パスを作成します。 パーティションがない場合、アクティビティはファイルを上書きします。

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. **AzureMLLinkedService** 型の**リンクされたサービス**を作成し、API キーとモデルのバッチ実行 URL を入力します。

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. 最後に、 **AzureMLBatchExecution** アクティビティを含むパイプラインを作成します。 実行時には、パイプラインは次の手順を実行します。

   1. 入力データセットから入力ファイルの場所を取得する。
   2. Azure Machine Learning のバッチ実行 API を呼び出す。
   3. バッチの実行出力を出力データセットで指定された BLOB にコピーする。

      > [!NOTE]
      > AzureMLBatchExecution アクティビティは、0 個以上の入力と 1 個以上の出力を使用できます。
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      **start** と [end](http://en.wikipedia.org/wiki/ISO_8601) の日時は、いずれも **ISO 形式**である必要があります。 (例: 2014-10-14T16:32:41Z)。 **start + 48 時間** " になります。 **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。 JSON のプロパティの詳細については、 [JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx) を参照してください。

      > [!NOTE]
      > AzureMLBatchExecution アクティビティへの入力の指定は省略可能です。
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>シナリオ: リーダー/ライター モジュールを使用してさまざまなストレージ内のデータを参照する
Azure ML を実験するときのもう 1 つの一般的なシナリオは、リーダー モジュールとライター モジュールを使用することです。 リーダー モジュールは実験にデータを読み込むために使用し、ライター モジュールは実験からデータを保存するために使用します。 リーダーとライター モジュールの詳細については、MSDN ライブラリの[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)と[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)に関するページを参照してください。     

リーダー/ライター モジュールの各プロパティには Web サービスのパラメーターを使用するのがよい方法です。 これらの Web パラメーターを使用すると、実行時に値を構成できます。 たとえば、Azure SQL Database: XXX.database.windows.net を使用するリーダー モジュールで実験を作成できます。 Web サービスをデプロイした後、Web サービスのコンシューマーを有効にして、YYY.database.windows.net という名前の別の Azure SQL Server を指定できます。 Web サービスのパラメーターを使用して、この値を構成できます。

> [!NOTE]
> Web サービスの入力と出力は、Web サービスのパラメーターとは異なるものです。 最初のシナリオでは、Azure ML Web サービスに対して入力と出力を指定する方法を説明しました。 このシナリオでは、リーダー/ライター モジュールのプロパティに対応するパラメーターを Web サービスに渡します。
>
>

Web サービス パラメーターを使用するシナリオを見てみましょう。 Azure Machine Learning でサポートされるいずれかのデータ ソース (例: Azure SQL Database) のデータをリーダー モジュールで読み取る Azure Machine Learning Web サービスをデプロイしました。 バッチ実行が実行された後、ライター モジュールを使用して結果が書き込まれます (Azure SQL Database)。  Web サービスの入力と出力は実験では定義されていません。 この場合は、リーダー/ライター モジュールに関連する Web サービス パラメーターを設定することをお勧めします。 この設定により、AzureMLBatchExecution アクティビティを使用するときにリーダー/ライター モジュールを構成できます。 Web サービスのパラメーターは、次に示すように、アクティビティの JSON の **globalParameters** セクションで指定します。

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

また、次の例に示すように、 [Data Factory 関数](data-factory-functions-variables.md) を使用して、Web サービス パラメーターの値を渡すこともできます。

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る
ビッグ データ パイプラインにアクティビティ (Pig、Hive など) を設定すると、拡張子が付いていない出力ファイルを 1 つ以上生成できます。 たとえば、外部 Hive テーブルを指定するとき、外部 Hive テーブルのデータを 000000_0 という名前で Azure BLOB ストレージに格納できます。 実験では、リーダー モジュールを使用して複数のファイルを読み取り、予測に使用できます。

Azure Machine Learning の実験でリーダー モジュールを使用する場合は、入力として Azure BLOB を指定できます。 Azure BLOB Strage 内のファイルは、HDInsight で実行する Pig および Hive スクリプトによって生成される出力ファイル (例: 000000_0) でもかまいません。 リーダー モジュールでは、 **[Path to container, directory/blob (コンテナーへのパス、ディレクトリ/BLOB)]**を構成して (拡張子がない) ファイルを読み取ることができます。 **コンテナーへのパス**でコンテナーをポイントし、**ディレクトリ/BLOB** では、次の画像のようにファイルが含まれるフォルダーをポイントします。 アスタリスク (\*) **は、実験の一環としてコンテナー/フォルダー内のすべてのファイル (つまり、data/aggregateddata/year=2014/month-6/\*)** を読み取るように指定します。

![Azure BLOB のプロパティ](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>例
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Web サービス パラメーターを使用した AzureMLBatchExecution のパイプライン

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

上の JSON の例に関する説明:

* デプロイされた Azure Machine Learning Web サービスは、リーダー モジュールとライター モジュールを使用して、Azure SQL Database のデータを読み書きします。 この Web サービスでは、Database server name、Database name、Server user account name、Server user account password という 4 つのパラメーターが公開されています。  
* **start** と [end](http://en.wikipedia.org/wiki/ISO_8601) の日時は、いずれも **ISO 形式**である必要があります。 (例: 2014-10-14T16:32:41Z)。 **start + 48 時間** " になります。 **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。 JSON のプロパティの詳細については、 [JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx) を参照してください。

### <a name="other-scenarios"></a>その他のシナリオ
#### <a name="web-service-requires-multiple-inputs"></a>Web サービスで複数の入力が必要である
Web サービスで複数の入力を受け取る場合は、**webServiceInput** プロパティを使用せずに、**webServiceInputs** プロパティを使用します。 **webServiceInputs** から参照されているデータセットもアクティビティの **inputs** に含める必要があります。

Azure ML の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure ML エンドポイントで必要なマッピングを確認することができます。

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Web サービスに入力が必要ではない
Azure ML バッチ実行 Web サービスを使用すると、R や Python のスクリプトなど、入力が必要ではない任意のワークフローを実行できます。 また、GlobalParameters を公開しない Reader モジュールを使用して構成する方法を実験することもできます。 この場合、AzureMLBatchExecution アクティビティは次のように構成されます。

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Web サービスに入力/出力が必要ではない
Azure ML Batch 実行 Web サービスに Web サービスの出力を構成しない場合があります。 この例では、Web サービスの入力も出力ありません。また、GlobalParameters も構成されていません。 アクティビティ自体に構成されている出力があっても、webServiceOutput として出力されません。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web サービスはリーダーとライターを使用し、他のアクティビティが成功した場合にのみアクティビティを実行する
Azure ML Web サービスのリーダーとライター モジュールは、GlobalParameters あり、またはなしで構成できます。 ただし、いくつかのアップストリーム処理が完了した場合のみデータセットの依存関係を使用してサービスを呼び出すパイプラインに、サービス呼び出しを埋め込むことができます。 また、この方法を使用して、バッチ実行が完了した後に別のアクションをトリガーすることもできます。 この場合、Web サービスの入力または出力として指定せずに、アクティビティの入力と出力を使用する依存関係を表現することができます。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**ここまでのポイント** は次のとおりです。

* 実験用エンドポイントが webServiceInput を使用している場合、そのエンドポイントは BLOB データセットで表され、アクティビティの入力と webServiceInput プロパティに含まれます。 使用していない場合、webServiceInput プロパティは省略されます。
* 実験用エンドポイントが webServiceOutput(s) を使用している場合、そのエンドポイントは BLOB データセットで表され、アクティビティの出力と webServiceOutputs プロパティに含まれます。 アクティビティの出力と webServiceOutputs は、実験の各出力名でマッピングされます。 使用していない場合、webServiceOutputs プロパティは省略されます。
* 実験用エンドポイントが globalParameter を公開している場合、アクティビティの globalParameters プロパティでキーと値のペアとして指定されます。 公開していない場合、globalParameters プロパティは省略されます。 キーは大文字と小文字が区別されます。 [Azure Data Factory 関数](data-factory-functions-variables.md) を値に使用することができます。
* アクティビティの入力と出力のプロパティには、アクティビティの typeProperties から参照せずに含めることができるその他のデータセットがあります。 このようなデータセットは実行の制御にスライスの依存関係を使用しますが、それ以外の場合は、AzureMLBatchExecution アクティビティから無視されます。


## <a name="updating-models-using-update-resource-activity"></a>更新リソース アクティビティを使用してモデルを更新する
時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの ML モデルでスコア付け Web サービスを更新する必要があります。 Web サービスを使用して Azure ML モデルの再トレーニングと更新を有効にするための標準的な手順を次に示します。

1. [Azure ML Studio](https://studio.azureml.net)で実験を作成します。
2. モデルの準備が整ったら、Azure ML Studio を使用して、**トレーニング実験**とスコア付け/**予測実験**の両方の Web サービスを発行します。

次の表で、この例で使用する Web サービスについて説明します。  詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](../machine-learning/machine-learning-retrain-models-programmatically.md) 」を参照してください。

| Web サービスの種類 | description |
|:--- |:--- |
| **トレーニング Web サービス** |トレーニング データを受信し、トレーニング済みのモデルを作成します。 再トレーニングの出力は、Azure Blob Storage 内の .ilearner ファイルになります。  **既定のエンドポイント** が、トレーニング実験を Web サービスとして発行するときに自動的に作成されます。 エンドポイントは複数作成することができますが、この例では、既定のエンドポイントのみを使用します。 |
| **スコア付け Web サービス** |ラベルの付いていないデータの例を受信し、予測を作成します。 予測の出力は、実験の構成に応じてさまざまな形式 (.csv ファイル、Azure SQL Database の行など) をとります。 既定のエンドポイントが、予測実験を Web サービスとして発行するときに自動的に作成されます。 **Azure Portal** を使用して、2 つ目の [更新可能な既定以外のエンドポイント](https://manage.windowsazure.com)を作成します。 エンドポイントは複数作成することができますが、この例では、更新可能な既定以外のエンドポイントを 1 つだけ使用します。 手順の詳細については、 [エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md) に関する記事を参照してください。 |

次の図は、Azure ML でのトレーニングとスコア付けのエンドポイントの関係を示しています。

![[Web サービス]](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**training web service** を使用して、2 つ目の **トレーニング Web サービス**。 トレーニング Web サービスの呼び出す方法は、データのスコア付け用 Azure ML Web サービス (スコア付け Web サービス) を呼び出す場合と同じです。 前のセクションで、Azure Data Factory パイプラインから Azure ML Web サービスを呼び出す方法について詳しく説明しています。

**scoring web service** を使用して、2 つ目の **Azure ML 更新リソース アクティビティ** を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。 前の表で説明したように、更新可能な既定以外のエンドポイントを作成して使用する必要があります。 データ ファクトリ内の既存のリンクされたサービスも、既定以外のエンドポイントを使用するように更新して、常に最新の再トレーニングを行ったモデルが使用されるようにする必要があります。

次のシナリオで詳細を説明します。 このシナリオでは、Azure Data Factory パイプラインから Azure ML モデルの再トレーニングと更新を行う例を示します。

### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>シナリオ: Azure ML モデルの再トレーニングと更新
このセクションでは、 **Azure ML バッチ実行アクティビティ** を使用してモデルの再トレーニングを行うサンプル パイプラインを示します。 このパイプラインでは、 **Azure ML 更新リソース アクティビティ** を使用したスコア付け Web サービスのモデルの更新も行います。 このセクションでは、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

サンプル パイプラインのダイアグラム ビューを次に示します。 ご覧のように、Azure ML バッチ実行アクティビティはトレーニングの入力を受け取り、トレーニングの出力 (iLearner ファイル) を作成します。 Azure ML 更新リソース アクティビティはトレーニングの出力を受け取り、スコア付け Web サービスのエンドポイントでモデルを更新します。 更新リソース アクティビティは出力を作成しません。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

#### <a name="azure-blob-storage-linked-service"></a>Azure BLOB ストレージのリンクされたサービス:
Azure Storage には次のデータが格納されています。

* トレーニング データ。 Azure ML トレーニング Web サービス用の入力データです。  
* iLearner ファイル。 Azure ML トレーニング Web サービスの出力です。 このファイルは更新リソース アクティビティへの入力としても使用します。  

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

#### <a name="training-input-dataset"></a>トレーニングの入力データセット:
次のデータセットは、Azure ML トレーニング Web サービス用の入力トレーニング データを示しています。 Azure ML バッチ実行アクティビティはこのデータセットを入力として使用します。

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
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

#### <a name="training-output-dataset"></a>トレーニングの出力データセット:
次のデータセットは、Azure ML トレーニング Web サービスの出力 iLearner ファイルを示しています。 Azure ML バッチ実行アクティビティがこのデータセットを作成します。 このデータセットは、Azure ML 更新リソース アクティビティへの入力としても使用されます。

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML トレーニング エンドポイント用のリンクされたサービス
次の JSON スニペットは、トレーニング Web サービスの既定のエンドポイントを示す Azure Machine Learning のリンクされたサービスを定義します。

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

**Azure ML Studio** で、次の操作を行い、**mlEndpoint** と **apiKey** の値を取得します。

1. 左側のメニューで **[Web サービス]** をクリックします。
2. Web サービスの一覧で、 **[トレーニング Web サービス]** をクリックします。
3. **[API キー]** ボックスの隣にあるコピー ボタンをクリックします。 クリップボードにコピーされた API キーを Data Factory JSON エディターに貼り付けます。
4. **Azure ML studio** で **[バッチ実行]** リンクをクリックします。
5. **[要求]** セクションの**要求 URI** をコピーして、Data Factory JSON エディターに貼り付けます。   

#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットは、スコア付け Web サービスの更新可能な既定以外のエンドポイントを参照する Azure Machine Learning のリンクされたサービスを定義します。  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

Azure ML のリンクされたサービスの作成とデプロイの前に、 [エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md) に関する記事に記載された手順に従い、スコア付け Web サービス用に 2 つ目の (更新可能な既定以外の) エンドポイントを作成します。

更新可能な既定以外のエンドポイントを作成したら、次の操作を行います。

* **[バッチ実行]** をクリックして、**mlEndpoint** JSON プロパティの URI の値を取得します。
* **[リソースの更新]** リンクをクリックして、**updateResourceEndpoint** JSON プロパティの URI の値を取得します。 API キーは、エンドポイントのページにあります (右下隅)。

![updatable endpoint](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

#### <a name="placeholder-output-dataset"></a>プレースホルダーの出力データセット:
Azure ML 更新リソース アクティビティでは出力は作成されません。 ただし、Azure Data Factory でパイプラインのスケジュールを制御するには出力データセットが必要です。 このため、この例ではダミー/プレースホルダーのデータセットを使用します。  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

#### <a name="pipeline"></a>パイプライン
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 Azure ML バッチ実行アクティビティはトレーニング データを入力として使用し、.iLearner ファイルを出力として作成します。 このアクティビティは、トレーニング Web サービス (Web サービスとして公開されたトレーニング実験) と入力トレーニング データを呼び出し、Web サービスから ilearner ファイルを受け取ります。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```

### <a name="reader-and-writer-modules"></a>リーダーとライター モジュール
Web サービス パラメーターの使用を伴う一般的なシナリオとして、Azure SQL のリーダーとライターを使用するシナリオがあります。 リーダー モジュールは、Azure Machine Learning Studio 外部のデータ管理サービスからデータを実験にロードするために使用します。 ライター モジュールは、実験のデータを Azure Machine Learning Studio 外部のデータ管理サービスに保存するために使用します。  

Azure BLOB と Azure SQL のリーダー/ライターの詳細については、MSDN ライブラリの[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)と[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)に関するページを参照してください。 前のセクションの例では、Azure BLOB リーダーと Azure BLOB ライターを使用しています。 ここでは、Azure SQL リーダーと Azure SQL ライターの使用について説明します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
**Q:** ビッグ データ パイプラインによって生成される複数のファイルがあります。 AzureMLBatchExecution アクティビティを使用して、すべてのファイルで処理できますか。

**A:** はい。 詳しくは、「 **リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る** 」をご覧ください。

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML バッチ スコアリング アクティビティ
**AzureMLBatchScoring** アクティビティを使用して Azure Machine Learning と統合している場合は、最新の **AzureMLBatchExecution** アクティビティを使用することをお勧めします。

AzureMLBatchExecution アクティビティは、Azure SDK および Azure PowerShell の 2015 年 8 月のリリースで導入されました。

AzureMLBatchScoring アクティビティを引き続き使用する場合は、このセクションを読んでください。  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>入力/出力に Azure Storage を使用する Azure ML バッチ スコアリング アクティビティ

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Web サービス パラメーター
Web サービス パラメーターの値を指定するには、パイプライン JSON の **AzureMLBatchScoringActivty** セクションに **typeProperties** セクションを追加します。次に例を示します。

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
また、次の例に示すように、 [Data Factory 関数](data-factory-functions-variables.md) を使用して、Web サービス パラメーターの値を渡すこともできます。

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。
>
>

## <a name="see-also"></a>関連項目
* [Azure ブログの投稿: Azure Data Factory と Azure Machine Learning の概要](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/



<!--HONumber=Dec16_HO3-->


