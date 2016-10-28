<properties 
	pageTitle="Azure Data Factory から Spark プログラムを呼び出す" 
	description="MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# Data Factory から Spark プログラムを起動する
## はじめに
Data Factory パイプラインで MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。この記事を読む前に、アクティビティの使用の詳細について、[MapReduce アクティビティ](data-factory-map-reduce.md)に関する記事をご覧ください。

## GitHub 上の Spark サンプル
[GitHub 上の Spark - Data Factory サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark)は、MapReduce アクティビティを使用して Spark プログラムを起動する方法を示しています。Spark プログラムは、単に、1 つの Azure BLOB コンテナーから別のコンテナーにデータをコピーします。

## Data Factory のエンティティ
**Spark-ADF/src/ADFJsons** フォルダーには、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) のファイルが含まれています。

このサンプルには、2 つの**リンクされたサービス**があります。Azure Storage と Azure HDInsight です。Azure ストレージの名前とキーの値を **StorageLinkedService.json** に指定し、clusterUri、userName、およびパスワードを **HDInsightLinkedService.json** に指定します。

このサンプルには、2 つの**データセット**があります。**input.json** と **output.json** です。これらのファイルは、**Datasets** フォルダーにあります。これらのファイルが表しているのは、MapReduce アクティビティの入力および出力データセットです。

**ADFJsons/Pipeline** フォルダーにはサンプル パイプラインがあります。パイプラインを確認して、MapReduce アクティビティを使用して Spark プログラムを呼び出す方法について理解します。

MapReduce アクティビティは、Azure ストレージの **adflibs** コンテナーの **com.adf.sparklauncher.jar** を呼び出すように構成されています (StorageLinkedService.json で指定)。このプログラムのソース コードは Spark-ADF/src/main/java/com/adf/ フォルダーにあり、spark-submit を呼び出して Spark ジョブを実行します。

> [AZURE.IMPORTANT] 
サンプルを使用する前に、[README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) に目を通して、最新情報と追加情報を確認してください。
>  
> MapReduce アクティビティを使用して Spark プログラムを呼び出すには、この方法で独自の HDInsight Spark クラスターを使用します。オンデマンド HDInsight クラスターの使用は、サポートされていません。


## 関連項目
- [Hive アクティビティ](data-factory-hive-activity.md)
- [Pig アクティビティ](data-factory-pig-activity.md)
- [MapReduce アクティビティ](data-factory-map-reduce.md)
- [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
- [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->