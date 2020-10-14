---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 5e7bdce3a195d8171fef1963f2e389bfeec5fffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571865"
---
Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態でパイプラインに追加できます。

| データ変換アクティビティ | Compute 環境 |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop ストリーミング](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Machine Learning アクティビティ: バッチ実行とリソース更新](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [ストアド プロシージャ](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics、または SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] または Azure Batch |

> [!NOTE]
> MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。 詳細については、「 [Invoke Spark programs from Azure Data Factory (Azure Data Factory から Spark プログラムを呼び出す)](../articles/data-factory/v1/data-factory-spark.md) 」を参照してください。
> カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)に関するトピックを参照してください。
> 
> 

