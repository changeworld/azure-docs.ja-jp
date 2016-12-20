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
ms.date: 11/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>Data Factory から Spark プログラムを起動する
## <a name="introduction"></a>はじめに
Data Factory パイプラインで MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。 この記事を読む前に、アクティビティの使用の詳細について、 [MapReduce アクティビティ](data-factory-map-reduce.md) に関する記事をご覧ください。 

## <a name="spark-sample-on-github"></a>GitHub 上の Spark サンプル
[GitHub 上の Spark - Data Factory サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) は、MapReduce アクティビティを使用して Spark プログラムを起動する方法を示しています。 Spark プログラムは、単に、1 つの Azure BLOB コンテナーから別のコンテナーにデータをコピーします。 

## <a name="data-factory-entities"></a>Data Factory のエンティティ
**Spark-ADF/src/ADFJsons** フォルダーには、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) のファイルが含まれています。  

このサンプルには、2 つの**リンクされたサービス**があります。Azure Storage と Azure HDInsight です。 Azure ストレージの名前とキーの値を **StorageLinkedService.json** に指定し、clusterUri、userName、およびパスワードを **HDInsightLinkedService.json** に指定します。

このサンプルには、2 つの**データセット**があります。**input.json** と **output.json** です。 これらのファイルは、**Datasets** フォルダーにあります。  これらのファイルが表しているのは、MapReduce アクティビティの入力および出力データセットです。

**ADFJsons/Pipeline** フォルダーにはサンプル パイプラインがあります。 パイプラインを確認して、MapReduce アクティビティを使用して Spark プログラムを呼び出す方法について理解します。 

MapReduce アクティビティは、Azure ストレージの **adflibs** コンテナーの **com.adf.sparklauncher.jar** を呼び出すように構成されています (StorageLinkedService.json で指定)。 このプログラムのソース コードは Spark-ADF/src/main/java/com/adf/ フォルダーにあり、spark-submit を呼び出して Spark ジョブを実行します。 

> [!IMPORTANT]
> サンプルを使用する前に、[README.md](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md) に目を通して、最新情報と追加情報を確認してください。 
> 
> MapReduce アクティビティを使用して Spark プログラムを呼び出すには、この方法で独自の HDInsight Spark クラスターを使用します。 オンデマンド HDInsight クラスターの使用は、サポートされていません。   
> 
> 

## <a name="see-also"></a>関連項目
* [Hive アクティビティ](data-factory-hive-activity.md)
* [Pig アクティビティ](data-factory-pig-activity.md)
* [MapReduce アクティビティ](data-factory-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


