---
title: データを変換する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics でのデータの変換またはデータの処理には、Hadoop、ML Studio (クラシック)、または Azure Data Lake Analytics を使用します。
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6a5500bdcf551fbbb8c3fc612606ec63bce00557
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805949"
---
# <a name="transform-data-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics でデータを変換する

> [!div class="op_single_selector"]
> * [マッピング データ フロー](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [ML Studio (クラシック)](transform-data-using-machine-learning.md) 
> * [ストアド プロシージャ](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Azure Synapse ノートブック](../synapse-analytics/synapse-notebook-activity.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET カスタム](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>概要
この記事では、Azure Data Factory および Synapse パイプラインでのデータ変換アクティビティについて説明します。これを使用すると、大規模な生データを予測と分析情報に変換して処理することができます。 変換アクティビティは、Azure Databricks や Azure HDInsight などのコンピューティング環境で実行されます。 各変換アクティビティの詳細情報に関する記事へのリンクが提供されています。

このサービスは、次のデータ変換アクティビティをサポートしています。これらは、個別にまたは他のアクティビティと連結して[パイプライン](concepts-pipelines-activities.md)に追加できます。

## <a name="transform-natively-in-azure-data-factory-and-azure-synapse-analytics-with-data-flows"></a>データ フローを使用して Azure Data Factory および Azure Synapse Analytics でネイティブに変換する

### <a name="mapping-data-flows"></a>データ フローのマッピング

マッピング データ フローは、Azure Data Factory および Azure Synapse における視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくグラフィカルなデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Spark クラスターを使用するパイプライン内のアクティビティとして実行されます。 データ フロー アクティビティは、サービス内の既存のスケジュール設定、制御、フロー、および監視機能を通して運用化できます。 詳細については、[マッピング データ フロー](concepts-data-flow-overview.md)に関するページを参照してください。

### <a name="data-wrangling"></a>データ ラングリング

Azure Data Factory の Power Query を使用すると、クラウド規模のデータ ラングリングを実行できます。これにより、コード不要のデータ準備をクラウド規模で反復的に実行できます。 データ ラングリングは [Power Query Online](/power-query/) と統合されており、Spark の実行によりクラウド規模で Power Query M 関数をデータ ラングリングに使用できるようにします。 詳細については、「[Azure Data Factory でのデータ ラングリング](wrangling-overview.md)」を参照してください。

> [!NOTE]
> Power Query は現在、Azure Data Factory でのみサポートされ、Azure Synapse ではサポートされていません。  各サービスでサポートされている具体的な機能の一覧については、[Azure Data Factory および Azure Synapse Analytics のパイプラインで使用できる機能](../synapse-analytics/data-integration/concepts-data-factory-differences.md)に関する記事を参照してください。

## <a name="external-transformations"></a>外部での変換

必要に応じて、変換を手動でコーディングし、外部のコンピューティング環境を自分で管理することもできます。

### <a name="hdinsight-hive-activity"></a>HDInsight Hive アクティビティ
パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 このアクティビティの詳細については、[Hive アクティビティ](transform-data-using-hadoop-hive.md)に関する記事をご覧ください。 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig アクティビティ
パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Pig クエリを実行します。 このアクティビティの詳細については、[Pig アクティビティ](transform-data-using-hadoop-pig.md)に関する記事をご覧ください。 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティ
パイプラインの HDInsight MapReduce アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 このアクティビティの詳細については、[MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)に関する記事をご覧ください。

### <a name="hdinsight-streaming-activity"></a>HDInsight Streaming アクティビティ
パイプラインの HDInsight Streaming アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hadoop Streaming プログラムを実行します。 このアクティビティの詳細については、記事「 [HDInsight Streaming アクティビティ](transform-data-using-hadoop-streaming.md) 」を参照してください。

### <a name="hdinsight-spark-activity"></a>HDInsight Spark アクティビティ
パイプラインの HDInsight Spark アクティビティでは、独自の HDInsight クラスターで Spark プログラムを実行します。 詳細については、[Azure Data Factory またはor Azure Synapse Analytics で Spark プログラムを呼び出す](transform-data-using-spark.md)ことに関する記事を参照してください。 

### <a name="ml-studio-classic-activities"></a>ML Studio (クラシック) アクティビティ
このサービスを使用すると、公開された ML Studio (クラシック) Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 パイプライン内で [Batch Execution アクティビティ](transform-data-using-machine-learning.md)を使用すると、Studio (クラシック) Web サービスを呼び出して、データの予測を一括で行うことができます。

時間の経過と共に、スタジオ (クラシック) スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの機械学習モデルでスコア付け Web サービスを更新する必要があります。 [更新リソース アクティビティ](update-machine-learning-models.md)を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

これらの Studio (クラシック) アクティビティの詳細については、[ML Studio (クラシック) アクティビティの使用](transform-data-using-machine-learning.md)に関するページを参照してください。 

### <a name="stored-procedure-activity"></a>ストアド プロシージャ アクティビティ
SQL Server ストアド プロシージャ アクティビティを Data Factory のパイプライン内で使用して、次のいずれかのデータ ストア内のストアド プロシージャを呼び出すことができます。企業または Azure VM 内の Azure SQL Database、Azure Synapse Analytics、SQL Server データベース。 詳細については、[ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)に関する記事をご覧ください。  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
Data Lake Analytics U-SQL アクティビティは、Azure Data Lake Analytics クラスターで U-SQL スクリプトを実行します。 詳細については、[Data Analytics U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)に関する記事をご覧ください。 

### <a name="azure-synapse-notebook-activity"></a>Azure Synapse Notebook アクティビティ 

Synapse パイプラインの Synapse Azure Synapse Notebook アクティビティは、Azure Synapse ワークスペースの Synapse Notebook を実行します。 [Azure Synapse Notebook を実行してデータを変換する](../synapse-analytics/synapse-notebook-activity.md)ことに関する記事を参照してください。

### <a name="databricks-notebook-activity"></a>Databricks Notebook アクティビティ

パイプラインの Azure Databricks Notebook アクティビティは、Azure Databricks ワークスペースで Databricks ノートブックを実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Databricks Notebook を実行してデータを変換する](transform-data-databricks-notebook.md)」を参照してください。

### <a name="databricks-jar-activity"></a>Databricks Jar アクティビティ

パイプラインの Azure Databricks Jar アクティビティは、Azure Databricks クラスターで Spark Jar を実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Jar アクティビティを実行してデータを変換する](transform-data-databricks-jar.md)」を参照してください。

### <a name="databricks-python-activity"></a>Databricks Python アクティビティ

パイプラインの Azure Databricks Python アクティビティは、Azure Databricks クラスターで Python ファイルを実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Python アクティビティを実行してデータを変換する](transform-data-databricks-python.md)」を参照してください。

### <a name="custom-activity"></a>カスタム アクティビティ
Data Factory でサポートされていない方法でデータを変換する必要がある場合は、独自のデータ処理ロジックを使用するカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。 Azure Batch サービスまたは Azure HDInsight クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。 [Use custom activities](transform-data-using-dotnet-custom-activity.md) (カスタム アクティビティの使用) を参照してください。 

カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory および Synapse パイプラインを使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)に関するトピックを参照してください。 

### <a name="compute-environments"></a>コンピューティング環境
変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用します。 サポートされているコンピューティング環境は 2 種類あります。 

- **オンデマンド**: この場合、コンピューティング環境はサービスで完全に管理されます。 データを処理するためのジョブが送信される前にサービスにより自動的に作成され、ジョブの完了時に削除されます。 ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
- **独自の環境を使用する**: この場合、独自のコンピューティング環境 (HDInsight クラスターなど) をリンク サービスとして登録できます。 このコンピューティング環境はユーザーにより管理され、サービスではこの環境を使用してアクティビティを実行します。 

サポートされるコンピューティング サービスの詳細については、「[コンピューティングのリンクされたサービス](compute-linked-services.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ
変換アクティビティの使用例については、次のチュートリアルをご覧ください: [チュートリアル: Spark を使用してデータを変換する](tutorial-transform-data-spark-powershell.md)