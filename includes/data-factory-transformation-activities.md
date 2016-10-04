Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態でパイプラインに追加できます。

データ変換アクティビティ | Compute 環境 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Machine Learning アクティビティ: バッチ実行と更新のリソース](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[ストアド プロシージャ](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL、Azure SQL Data Warehouse、または SQL Server |
[Data Lake Analytics U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] または Azure Batch
   
> [AZURE.NOTE] 
MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。詳細については、[Data Factory からの Spark プログラムの呼び出し](../articles/data-factory/data-factory-spark.md)に関するページを参照してください。カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。[Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)に関するトピックを参照してください。

<!---HONumber=AcomDC_0928_2016-->