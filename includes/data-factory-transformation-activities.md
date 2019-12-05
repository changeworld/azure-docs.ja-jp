---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: e6a1e65f253f33257a0e218f4d347740c07581cc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796178"
---
Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態でパイプラインに追加できます。

| データ変換アクティビティ | Compute 環境 |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop ストリーミング](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Machine Learning アクティビティ:バッチ実行とリソースの更新](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [ストアド プロシージャ](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL、Azure SQL Data Warehouse、または SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] または Azure Batch |

> [!NOTE]
> MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。 詳細については、 [Data Factory からの Spark プログラムの呼び出し](../articles/data-factory/v1/data-factory-spark.md) に関するページを参照してください。
> カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)に関するトピックを参照してください。
> 
> 

