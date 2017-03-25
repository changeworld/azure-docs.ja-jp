---
title: "Azure Data Factory から Spark プログラムを呼び出す"
description: "MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2af5d275bb331101b370e4a12043e27b6cdf5b68
ms.lasthandoff: 03/15/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Data Factory から Spark プログラムを起動する
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

## <a name="introduction"></a>はじめに
Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Spark アクティビティでは、[独自の](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight クラスターで Spark プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

## <a name="hdinsight-linked-service"></a>HDInsight のリンクされたサービス
Data Factory パイプラインで Spark アクティビティを使用する前に、(ご自身の) HDInsight のリンクされたサービスを作成します。 次の JSON スニペットは、ご自身の Azure HDInsight Spark クラスターを指定する、HDInsight のリンクされたサービスの定義を示しています。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 現在、Spark アクティビティでは、Data Lake Store をプライマリ ストレージとして使用した Spark クラスター、または HDInsight のオンデマンドのリンクされたサービスはサポートされません。 

HDInsight のリンクされたサービスおよびその他のコンピューティングのリンクされたサービスの詳細については、[Data Factory コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)に関するページをご覧ください。 

## <a name="spark-activity-json"></a>Spark アクティビティ JSON
Spark アクティビティのサンプルの JSON 定義を次に示します。    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
次の表で、JSON 定義で使用される JSON プロパティについて説明します。 

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| name | パイプラインのアクティビティの名前。 | はい |
| 説明 | アクティビティの動作を説明するテキスト。 | いいえ |
| type | このプロパティは HDInsightSpark に設定する必要があります。 | はい |
| 既定のコンテナー | Spark プログラムが実行されている HDInsight のリンクされたサービスへの参照。 | はい |
| rootPath | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 | はい |
| entryFilePath | Spark コード/パッケージのルート フォルダーへの相対パス | はい |
| className | アプリケーションの Java/Spark のメイン クラス | なし | 
| arguments | Spark プログラムのコマンドライン引数の一覧です。 | いいえ | 
| proxyUser | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ | 
| sparkConfig | Spark の構成のプロパティ | いいえ | 
| getDebugInfo | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | なし | 
| sparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | なし |

## <a name="folder-structure"></a>フォルダー構造
Spark アクティビティでは、Pig および Hive アクティビティが実行するインライン スクリプトがサポートされません。 また、Spark ジョブは、Pig/Hive ジョブよりも拡張できます。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス | Description | 必須 | 型 |
| ---- | ----------- | -------- | ---- | 
| をクリックします。    | ストレージのリンクされたサービスにおける Spark ジョブのルート パス    | はい | フォルダー |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | はい | ファイル | 
| ./jars | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | なし | フォルダー |
| ./pyFiles | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ | フォルダー |
| ./files | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ | フォルダー |
| ./archives | このフォルダーのファイルは圧縮されていません | なし | フォルダー |
| ./logs | Spark クラスターのログが格納されているフォルダー。| なし | フォルダー |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Azure Blob Storage に&2; つの Spark ジョブ ファイルが含まれています。 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> 変換アクティビティでパイプラインを作成する方法をさらに詳しく解説したチュートリアルについては、[データを変換するパイプラインの作成](data-factory-build-your-first-pipeline-using-editor.md)に関する記事をご覧ください。 

## <a name="spark-sample-on-github"></a>GitHub 上の Spark サンプル
Spark アクティビティがサポートされる前は、MapReduce アクティビティを使用して、Spark プログラムを Data Factory パイプラインから実行していました。 引き続き Data Factory パイプラインで [MapReduce アクティビティ](data-factory-map-reduce.md)を使用して、HDInsight Spark クラスターで Spark プログラムを実行できますが、 MapReduce アクティビティではなく、Spark アクティビティを使用することをお勧めします。 

[GitHub 上の Spark - Data Factory サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) は、MapReduce アクティビティを使用して Spark プログラムを起動する方法を示しています。 Spark プログラムは、単に、1 つの Azure BLOB コンテナーから別のコンテナーにデータをコピーします。 

## <a name="see-also"></a>関連項目
* [Hive アクティビティ](data-factory-hive-activity.md)
* [Pig アクティビティ](data-factory-pig-activity.md)
* [MapReduce アクティビティ](data-factory-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


