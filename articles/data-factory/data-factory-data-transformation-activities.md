<properties 
	pageTitle="データの変換: データの処理と変換 | Microsoft Azure" 
	description="Azure Data Factory でのデータ変換について説明します。Azure HDInsight クラスターまたは Azure Batch のデータを変換し、処理します。" 
	keywords="データの変換, データの処理, データ変換, 変換アクティビティ"
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Azure Data Factory 内のデータの変換と分析について
この記事では、Azure Data Factory でのデータ変換アクティビティについて説明します。生データが ADF によってどのように変換、処理され、予測や洞察に利用されるかを学ぶことができます。変換アクティビティは、Azure HDInsight クラスターや Azure Batch などのコンピューティング環境で実行されます。特定の変換アクティビティの使用方法を示すいくつかの記事へのリンクも含まれています。
 
Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態で[パイプライン](data-factory-create-pipelines.md)に追加できます。

データ変換アクティビティ | Compute 環境 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Machine Learning アクティビティ: バッチ実行と更新のリソース](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[ストアド プロシージャ](data-factory-stored-proc-activity.md) | Azure SQL、Azure SQL Data Warehouse、または SQL Server |
[Data Lake Analytics U-SQL](data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] または Azure Batch
   
> [AZURE.NOTE] 
MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。詳細については、[Data Factory からの Spark プログラムの呼び出し](data-factory-spark.md)に関するページを参照してください。カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。[Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)に関するトピックを参照してください。
 

変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用する必要があります。Data Factory でサポートされているコンピューティング環境は 2 種類あります。

1. **オンデマンド**: この場合、コンピューティング環境は Data Factory で完全に管理されます。環境はデータを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。
2. **独自の環境を使用する**: この場合、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。

Data Factory でサポートされているコンピューティングにリンクされたサービスの詳細については、記事「[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)」を参照してください。

<!---HONumber=AcomDC_0629_2016-->