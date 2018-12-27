---
title: Azure Data Factory で Spark アクティビティを使用してデータを変換する | Microsoft Docs
description: Spark アクティビティを使用して、Azure Data Factory パイプラインから Spark プログラムを実行することによってデータを変換する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: douglasl
ms.openlocfilehash: abe2fabc505f94f19d4b15a406fc59bf6d6e7ac1
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050337"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory での Spark アクティビティを使用したデータの変換
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-spark.md)
> * [現在のバージョン](transform-data-using-spark.md)

Data Factory [パイプライン](concepts-pipelines-activities.md)の Spark アクティビティでは、[独自の](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンドの](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight クラスターで Spark プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 オンデマンドの Spark のリンクされたサービスを使用すると、Data Factory は自動的に Spark クラスターを作成し、ジャストインタイムでデータを処理し、処理が完了するとクラスターを削除します。 

> [!IMPORTANT]
> Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する HDInsight Spark クラスターはサポートされません。

## <a name="spark-activity-properties"></a>Spark アクティビティのプロパティ
Spark アクティビティのサンプルの JSON 定義を次に示します。    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

| プロパティ              | 説明                              | 必須 |
| --------------------- | ---------------------------------------- | -------- |
| name                  | パイプラインのアクティビティの名前。    | [はい]      |
| description            | アクティビティの動作を説明するテキスト。  | いいえ        |
| type                  | Spark アクティビティの場合、アクティビティの種類は HDInsightSpark です。 | [はい]      |
| linkedServiceName     | Spark プログラムが実行されている HDInsight Spark のリンクされたサービスの名前。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | [はい]      |
| SparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 このプロパティの値には、Azure Storage のリンクされたサービスのみを指定できます。 | いいえ        |
| rootPath              | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 このフォルダーの構造の詳細については、「フォルダー構造」(次のセクション) をご覧ください。 | [はい]      |
| entryFilePath         | Spark コード/パッケージのルート フォルダーへの相対パス。 エントリ ファイルは、Python ファイルまたは .jar ファイルのいずれかにする必要があります。 | [はい]      |
| className             | アプリケーションの Java/Spark のメイン クラス      | いいえ        |
| arguments             | Spark プログラムのコマンドライン引数の一覧です。 | いいえ        |
| proxyUser             | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ        |
| sparkConfig           | 「[Spark Configuration - Application properties (Spark 構成 - アプリケーションのプロパティ)](https://spark.apache.org/docs/latest/configuration.html#available-properties)」と題するトピックに示されている Spark 構成プロパティの値を指定します。 | いいえ        |
| getDebugInfo          | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | いいえ        |

## <a name="folder-structure"></a>フォルダー構造
Spark ジョブは、Pig/Hive ジョブよりも拡張性に優れています。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス                  | 説明                              | 必須 | type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (ルート)            | ストレージのリンクされたサービスにおける Spark ジョブのルート パス | [はい]      | フォルダー |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | [はい]      | ファイル   |
| ./jars                | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | いいえ        | フォルダー |
| ./pyFiles             | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ        | フォルダー |
| ./files               | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ        | フォルダー |
| ./archives            | このフォルダーのファイルは圧縮されていません | いいえ        | フォルダー |
| ./logs                | Spark クラスターのログが格納されているフォルダー。 | いいえ        | フォルダー |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Azure Blob Storage に 2 つの Spark ジョブ ファイルが含まれています。

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
## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
