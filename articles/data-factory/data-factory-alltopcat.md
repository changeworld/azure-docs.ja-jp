---
title: Data Factory サービスに関するすべてのトピック | Microsoft Docs
description: http://azure.microsoft.com/documentation/articles/ に存在する Data Factory という名前の Azure サービスに関するすべてのトピックの一覧 (タイトルと説明)。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: MightyPen

ms.service: data-factory
ms.workload: data-factory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: spelluru

---
# <a name="all-topics-for-azure-data-factory-service"></a>Azure Data Factory サービスに関するすべてのトピック
このトピックでは、Azure **Data Factory** サービスに直接適用されるすべてのトピックを示します。 この Web ページでは、 **Ctrl + F**を使用してキーワード検索することで、現在関心があるトピックを見つけることができます。

## <a name="new"></a>新規
| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 1 |[Azure Data Factory を使用して Amazon Redshift からデータを移動する](data-factory-amazon-redshift-connector.md) |Azure Data Factory を使用して Amazon Redshift からデータを移動する方法を説明します。 |
| 2 |[Azure Data Factory を使用した Amazon Simple Storage Service からのデータの移動](data-factory-amazon-simple-storage-service-connector.md) |Azure Data Factory を使用した Amazon Simple Storage Service (S3) からのデータの移動方法について説明します。 |
| 3 |[Azure Data Factory - コピー ウィザード](data-factory-azure-copy-wizard.md) |Azure Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。 |
| 4 |[チュートリアル: Data Factory REST API を使用した初めての Azure データ ファクトリの作成](data-factory-build-your-first-pipeline-using-rest-api.md) |このチュートリアルでは、Data Factory REST API を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 5 |[チュートリアル: コピー アクティビティがあるパイプラインを .NET API で作成する](data-factory-copy-activity-tutorial-using-dotnet-api.md) |このチュートリアルでは、.NET API を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 6 |[チュートリアル: コピー アクティビティがあるパイプラインを REST API で作成する](data-factory-copy-activity-tutorial-using-rest-api.md) |このチュートリアルでは、REST API を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 7 |[Data Factory コピー ウィザード](data-factory-copy-wizard.md) |Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。 |
| 8 |[Data Management Gateway](data-factory-data-management-gateway.md) |オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。 Azure Data Factory で Data Management Gateway を使用してデータを移動します。 |
| 9 |[Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する](data-factory-onprem-cassandra-connector.md) |Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する方法について説明します。 |
| 10 |[Azure Data Factory を使用して MongoDB からデータを移動する](data-factory-on-premises-mongodb-connector.md) |Azure Data Factory を使用して MongoDB データベースからデータを移動する方法を説明します。 |
| 11 |[Azure Data Factory を使用して Salesforce からデータを移動する](data-factory-salesforce-connector.md) |Azure Data Factory を使用して Salesforce からデータを移動する方法を説明します。 |

## <a name="updated-articles,-data-factory"></a>Data Factory の更新された記事
このセクションでは、最近、大幅な更新または重要な更新が行われた記事を示します。 更新された各記事について、追加されたマークダウン テキストが大まかに抜粋されています。 これらの記事は、**2016 年 8 月 22 日**～ **2016 年 10 月 5 日**に更新されたものです。

| &nbsp; | 記事 | 更新されたテキスト (抜粋) | 更新日 |
| ---:|:--- |:--- |:--- |
| 12 |[Azure Data Factory - .NET API の変更ログ](data-factory-api-change-log.md) |この記事では、特定のバージョンの Azure Data Factory SDK の変更に関する情報を提供します。 Azure Data Factory の最新の NuGet パッケージについては、こちらをご覧ください。(https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **バージョン 4.11.0** 機能の追加: / 次のリンクされたサービスの種類が追加されています。-  OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx)    -  AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx)   -  AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / 次のデータセットの型が追加されています。-  MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx)  -  AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / 次のコピー ソースの種類が追加されています。-  MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) ** バージョン 4.10.0** / 次の省略可能なプロパティが TextFormat に追加されました。-  SkipLineCount   -  FirstRowAsHeader   -  TreatEmptyAsNull |2016-09-22 |
| 13 |[Azure Data Factory を使用した Azure BLOB との間でのデータの移動](data-factory-azure-blob-connector.md) |/  copyBehavior  /  ソースが BlobSource または FileSystem である場合のコピー動作を定義します。  /  **PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスは、ターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。.br/..br/.**FlattenHierarchy:** ソース フォルダーのすべてのファイルがターゲット フォルダーの最上位レベルに配置されます。 ターゲット ファイルは、自動生成された名前になります。 .br/..br/.**MergeFiles: (既定値)** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。  /  No  /  **BlobSource** は、下位互換性のために次の 2 つのプロパティもサポートしています。 / **treatEmptyAsNull**: null または空の文字列を null 値として処理するかどうかを指定します。 / **skipHeaderLineCount** - スキップする必要がある行数を指定します。 入力データセットで TextFormat を利用しているときにのみ適用されます。 同様に、**BlobSink** は以下をサポートしています。 |2016-09-28 |
| 14 |[Azure Machine Learning アクティビティを使用して予測パイプラインを作成する](data-factory-azure-ml-batch-execution-activity.md) |**Web サービスで複数の入力が必要である** Web サービスで複数の入力を受け取る場合は、**webServiceInput** プロパティを使用せずに、**webServiceInputs** プロパティを使用します。 **webServiceInputs** から参照されているデータセットもアクティビティの **inputs** に含める必要があります。 Azure ML の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure ML エンドポイントで必要なマッピングを確認することができます。    {       "name": "PredictivePipeline",       "properties": {             "description": "use AzureML model",             "activities":  {                "name": "MLActivity",               "type": "AzureMLBatchExecution",                "description": "prediction analysis on batch input",                "inputs":  {                    "name": "inputDataset1"                 }, {                    "name": "inputDatase |2016-09-13 |
| 15 |[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md) |1.**ベースラインを確立する**。 開発フェーズでは、代表的なデータ サンプルに対してコピー アクティビティを使用して、パイプラインをテストします。 Data Factory のスライシング モデル (data-factory-scheduling-and-execution.md time-series-datasets-and-data-slices) を使用することで、操作するデータの量を制限できます。  **監視と管理アプリ**を使用して、実行時間とパフォーマンス特性を収集します。 Data Factory のホーム ページで、**[監視と管理]** を選択します。 ツリー ビューで、 **出力データセット**を選択します。 **[Activity Windows (アクティビティ ウィンドウ)]** の一覧で、コピー アクティビティの実行を選択します。 **[Activity Windows (アクティビティ ウィンドウ)]** には、コピー アクティビティの期間とコピーされるデータのサイズが表示されます。 スループットは、 **[Activity Window Explorer (アクティビティ ウィンドウ エクスプローラー)]**に一覧表示されます。 このアプリの詳細については、新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理 (data-factory-monitor-manage-app.md) に関する記事を参照してください。  ! アクティビティ実行の詳細 (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn |2016-09-27 |
| 16 |[チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する](data-factory-copy-activity-tutorial-using-visual-studio.md) |次の点に注意してください。- データセットの **type** が **AzureBlob** に設定されています。     - **linkedServiceName** が **AzureStorageLinkedService** に設定されています。 このリンクされたサービスは手順 2. で作成しました。     - **folderPath** が **adftutorial** コンテナーに設定されています。 **fileName** プロパティを使用して、フォルダー内の BLOB の名前を指定することもできます。 BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。    - format の **type** が **TextFormat** に設定されています。- テキスト ファイル内に 2 つのフィールド (**FirstName** と **LastName**) があり、コンマ (**columnDelimiter**) で区切られています。- **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval **は **1** に設定されています)。 そのため、Data Factory は、指定された BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを 1 時間ごとに検索します。  **入力**データセット用に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。 |2016-09-29 |
| 17 |[Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する](data-factory-create-data-factories-programmatically.md) |アプリケーション ID とパスワード (クライアント シークレット) を書き留めて、チュートリアルで使用します。 **Azure サブスクリプションとテナント ID を入手する**  コンピューターに PowerShell の最新バージョンをインストールしていない場合は、「Azure PowerShell のインストールおよび構成方法」(../powershell-install-configure.md) に記載されている手順に従ってインストールします。 1. Azure PowerShell を起動し、次のコマンドを実行します。2. 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。         Login-AzureRmAccountこのアカウントに関連付けられている Azure サブスクリプションを 1 つのみをお持ちの場合は、次の 2 つの手順を実行する必要はありません。 3. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。       Get-AzureRmSubscription4. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **NameOfAzureSubscription** を自分の Azure サブスクリプションの名前で置き換えます。       Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription   /  Set-AzureRmCo |2016-09-14 |
| 18 |[Azure Data Factory のパイプラインとアクティビティ](data-factory-create-pipelines.md) |,       "start": "2016-07-12T00:00:00Z",    "end": "2016-07-13T00:00:00Z"       }     } 次の点に注意してください。 / activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。 / アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。 / **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 このパイプライン作成の完全なチュートリアルについては、「Blob Storage から SQL Database へのデータ コピーのチュートリアル」 (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) をご覧ください。 **変換パイプラインのサンプル** 次のサンプル パイプラインでは、**アクティビティ** セクションに **HDInsightHive** 型アクティビティが 1 つあります。 このサンプルでは、HDInsight Hive アクティビティ (data-factory-hive-activity.md) が、Azure HDInsight Hadoop クラスターで Hive スクリプト ファイルを実行して、Azure Blob Storage からデータを変換します。  {     "name": "TransformPipeline",    "p |2016-09-27 |
| 19 |[Azure Data Factory でデータを変換する](data-factory-data-transformation-activities.md) |Data Factory は、次のデータ変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態でパイプライン (data-factory-create-pipelines.md) に追加できます。 で SQL Server VM を作成および管理することもできます。  AZURE.NOTE  具体的な手順を示すチュートリアルについては、Hive 変換を使用するパイプラインを作成する (data-factory-build-your-first-pipeline.md) 記事を参照してください。 **HDInsight Hive アクティビティ** Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 このアクティビティの詳細については、「Hive アクティビティ」 (data-factory-hive-activity.md) を参照してください。 **HDInsight Pig アクティビティ** Data Factory パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。 このアクティビティの詳細については、「Pig アクティビティ 」 (data-factory-pig-activity.md) を参照してください。 **HDInsight MapReduce アクティビティ** Data Factory パイプラインの HDInsight MapReduce アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 |2016-09-26 |
| 20 |[Data Factory のスケジュール設定と実行](data-factory-scheduling-and-execution.md) |CopyActivity2 は、CopyActivity1 が正常に実行していて、Dataset2 を使用できた場合にのみ実行します。 パイプライン JSON の例を次に示します。 {       "name": "ChainActivities",    "properties": {           "description": "Run activities in sequence",      "activities":       {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "copyBehavior": "PreserveHierarchy",    "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "inputs":       {       "name": "Dataset1"      }       ,       "outputs":      {       "name": "Dataset2"      }       ,       "policy": {     "timeout": "01:00:00"       },      "scheduler": {      "frequency": "Hour",    "interval": 1       },      "name": "CopyFromBlob1ToBlob2",     "description": "Copy data from a blob to another"       },      {       "type": "Copy",     "typeProperties": {     "source": {     "type": "BlobSource"    },      "sink": {       "type": "BlobSink",     "writeBatchSize": 0,    "writeBatchTimeout": "00:00:00"     }       },      "in |2016-09-28 |

## <a name="tutorials"></a>Tutorials (チュートリアル)
| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 21 |[チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md) |この Azure Data Factory のチュートリアルでは、Hadoop クラスターで Hive スクリプトを使用してデータを処理するデータ ファクトリを作成およびスケジュールする方法を示します。 |
| 22 |[チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-arm.md) |このチュートリアルでは、Azure Resource Manager テンプレートを使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 23 |[チュートリアル: Azure Portal を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-editor.md) |このチュートリアルでは、Azure ポータルで Data Factory エディターを使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 24 |[チュートリアル: Azure PowerShell を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-powershell.md) |このチュートリアルでは、Azure PowerShell を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 25 |[チュートリアル: Microsoft Visual Studio を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-vs.md) |このチュートリアルでは、Visual Studio を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 26 |[チュートリアル: コピー アクティビティがあるパイプラインを Azure Portal で作成する](data-factory-copy-activity-tutorial-using-azure-portal.md) |このチュートリアルでは、Azure ポータルで Data Factory エディターを使用して、コピー アクティビティを含む Azure Data Factory パイプラインを作成します。 |
| 27 |[チュートリアル: コピー アクティビティがあるパイプラインを Azure PowerShell で作成する](data-factory-copy-activity-tutorial-using-powershell.md) |このチュートリアルでは、Azure PowerShell を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 28 |[チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する](data-factory-copy-activity-tutorial-using-visual-studio.md) |このチュートリアルでは、Visual Studio を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 29 |[Data Factory を使用した Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |このチュートリアルでは、Azure Data Factory パイプラインでコピー アクティビティを使用して、Blob Storage から SQL Database にデータをコピーする方法を示します。 |
| 30 |[チュートリアル: コピー アクティビティがあるパイプラインを Data Factory コピー ウィザードで作成する](data-factory-copy-data-wizard-tutorial.md) |このチュートリアルでは、Data Factory でサポートされているコピー ウィザードを使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |

## <a name="data-movement"></a>データの移動
| &nbsp; | タイトル | Description |
| ---:|:--- |:--- |
| 31 |[Azure Data Factory を使用した Azure BLOB との間でのデータの移動](data-factory-azure-blob-connector.md) |Azure Data Factory で BLOB データをコピーする方法について説明します。 サンプルを使用して、Azure Blob Storage と Azure SQL Database の間でデータをコピーする方法を示します。 |
| 32 |[Azure Data Factory を使用した Azure Data Lake Store との間でのデータの移動](data-factory-azure-datalake-connector.md) |Azure Data Factory を使用して Azure Data Lake Store に、または Azure Data Lake Store からデータを移動する方法を説明します。 |
| 33 |[Azure Data Factory を使用した DocumentDB との間でのデータの移動](data-factory-azure-documentdb-connector.md) |Azure Data Factory を使用して Azure DocumentDB コレクションに、または Azure DocumentDB コレクションからデータを移動する方法を説明します。 |
| 34 |[Azure Data Factory を使用した Azure SQL Database との間でのデータの移動](data-factory-azure-sql-connector.md) |Azure Data Factory を使用して Azure SQL Database に、または Azure SQL Database からデータを移動する方法を説明します。 |
| 35 |[Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動](data-factory-azure-sql-data-warehouse-connector.md) |Azure Data Factory を使用して Azure SQL Data Warehouse に、または Azure SQL Data Warehouse からデータを移動する方法を説明します。 |
| 36 |[Azure Data Factory を使用した Azure テーブルとの間でのデータの移動](data-factory-azure-table-connector.md) |Azure Data Factory を使用して Azure Table Storage に、または Azure Table Storage からデータを移動する方法を説明します。 |
| 37 |[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md) |コピー アクティビティを使用する場合に、Azure Data Factory でのデータ移動のパフォーマンスに影響する主な要因について説明します。 |
| 38 |[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md) |Data Factory パイプラインでのデータの移動 (クラウド ストア間、およびオンプレミスのストアとクラウド ストアの間でのデータ移行) について説明します。 コピー アクティビティの使用。 |
| 39 |[Data Management Gateway のリリース ノート](data-factory-gateway-release-notes.md) |Data Management Gateway のリリース ノート |
| 40 |[Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する](data-factory-hdfs-connector.md) |Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する方法を説明します。 |
| 41 |[新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md) |監視と管理アプリを使用して Azure のデータ ファクトリとパイプラインを監視および管理する方法について説明します。 |
| 42 |[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) |オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。 Azure Data Factory で Data Management Gateway を使用してデータを移動します。 |
| 43 |[Azure Data Factory を使用して OData ソースからデータを移動する](data-factory-odata-connector.md) |Azure Data Factory を使用して OData ソースからデータを移動する方法を説明します。 |
| 44 |[Azure Data Factory を使用して ODBC データ ストアからデータを移動する](data-factory-odbc-connector.md) |Azure Data Factory を使用して ODBC データ ストアからデータを移動する方法を説明します。 |
| 45 |[Azure Data Factory を使用して DB2 からデータを移動する](data-factory-onprem-db2-connector.md) |Azure Data Factory を使用して DB2 データベースからデータを移動する方法を説明します |
| 46 |[Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する](data-factory-onprem-file-system-connector.md) |Azure Data Factory を使用して、オンプレミスのファイル システムとの間でデータを移動する方法について説明します。 |
| 47 |[Azure Data Factory を使用して MySQL からデータを移動する](data-factory-onprem-mysql-connector.md) |Azure Data Factory を使用して MySQL データベースからデータを移動する方法を説明します。 |
| 48 |[Azure Data Factory を使用してオンプレミスの Oracle との間でデータを移動する](data-factory-onprem-oracle-connector.md) |Azure Data Factory を使用してオンプレミスの Oracle データベースとの間でデータを移動する方法を説明します。 |
| 49 |[Azure Data Factory を使用して PostgreSQL からデータを移動する](data-factory-onprem-postgresql-connector.md) |Azure Data Factory を使用して PostgreSQL Databases からデータを移動する方法を説明します。 |
| 50 |[Azure Data Factory を使用して Sybase からデータを移動する](data-factory-onprem-sybase-connector.md) |Azure Data Factory を使用して Sybase データベースからデータを移動する方法を説明します。 |
| 51 |[Azure Data Factory を使用して Teradata からデータを移動する](data-factory-onprem-teradata-connector.md) |Teradata データベースからデータを移動できる Data Factory サービスの Teradata コネクタについて学習する |
| 52 |[Azure Data Factory を使用してオンプレミスまたは IaaS (Azure VM) の SQL Server との間でデータを移動する](data-factory-sqlserver-connector.md) |Azure Data Factory を使用してオンプレミスまたは Azure VM の SQL Server データベースとの間でデータを移動する方法を説明します。 |
| 53 |[Azure Data Factory を使用して Web テーブル ソースからデータを移動する](data-factory-web-table-connector.md) |Azure Data Factory を使用してオンプレミスのテーブルからデータを移動する方法を説明します。 |

## <a name="data-transformation"></a>データ操作
| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 54 |[Azure Machine Learning アクティビティを使用して予測パイプラインを作成する](data-factory-azure-ml-batch-execution-activity.md) |Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します |
| 55 |[計算のリンクされたサービス](data-factory-compute-linked-services.md) |Azure Data Factory パイプラインでデータの変換/処理に使用できるコンピューティング環境について学習します。 |
| 56 |[Data Factory と Batch を使用して大規模なデータセットを処理する](data-factory-data-processing-using-batch.md) |Azure Batch の並列処理機能を使用して、Azure Data Factory パイプラインで膨大な量のデータを処理する方法について説明します。 |
| 57 |[Azure Data Factory でデータを変換する](data-factory-data-transformation-activities.md) |Azure Data Factory で Hadoop、Machine Learning、または Azure Data Lake Analytics を使用してデータを変換または処理する方法について説明します。 |
| 58 |[Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md) |Azure Data Factory で Hadoop ストリーミング アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hadoop ストリーミング プログラミングを実行する方法について説明します。 |
| 59 |[Hive アクティビティ](data-factory-hive-activity.md) |Azure データ ファクトリで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。 |
| 60 |[Data Factory から MapReduce プログラムを起動する](data-factory-map-reduce.md) |Azure Data Factory から Azure HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。 |
| 61 |[Pig アクティビティ](data-factory-pig-activity.md) |Azure データ ファクトリで Pig アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Pig スクリプトを実行する方法について説明します。 |
| 62 |[Data Factory から Spark プログラムを起動する](data-factory-spark.md) |MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。 |
| 63 |[SQL Server ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) |SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。 |
| 64 |[Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md) |カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。 |
| 65 |[Azure Data Factory から Azure Data Lake Analytics で U-SQL スクリプトを実行する](data-factory-usql-activity.md) |Azure Data Lake Analytics コンピューティング サービスで U-SQL スクリプトを実行してデータを処理する方法について説明します。 |

## <a name="samples"></a>サンプル
| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 66 |[Azure Data Factory - サンプル](data-factory-samples.md) |Azure Data Factory サービスに付属するサンプルについて詳細に説明します。 |

## <a name="use-cases"></a>ユース ケース
| &nbsp; | タイトル | Description |
| ---:|:--- |:--- |
| 67 |[お客様導入事例](data-factory-customer-case-studies.md) |お客様がどのように Azure Data Factory を使用しているかについて、いくつか紹介します。 |
| 68 |[使用事例 - 顧客プロファイル](data-factory-customer-profiling-usecase.md) |Azure Data Factory を使用して、データ駆動型ワークフロー (パイプライン) を作成し、顧客のゲーム会社をプロファイリングする方法について説明します。 |
| 69 |[使用事例 - 製品に関する推奨事項](data-factory-product-reco-usecase.md) |Azure Data Factory と他のサービスを使用して実装した使用事例について説明します。 |

## <a name="monitor-and-manage"></a>監視と管理
| &nbsp; | タイトル | Description |
| ---:|:--- |:--- |
| 70 |[Azure Data Factory のパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) |Azure ポータルおよび Azure PowerShell を使用して、作成した Azure Data Factory とパイプラインを監視および管理する方法について説明します。 |

## <a name="sdk"></a>SDK
| &nbsp; | タイトル | Description |
| ---:|:--- |:--- |
| 71 |[Azure Data Factory - .NET API の変更ログ](data-factory-api-change-log.md) |Azure Data Factory.の特定のバージョンの .NET API における重大な変更、機能の追加、バグ修正などについて説明します。 |
| 72 |[Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する](data-factory-create-data-factories-programmatically.md) |Data Factory SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理する方法について説明します。 |
| 73 |[Azure Data Factory の開発者用リファレンス](data-factory-sdks.md) |Azure Data Factory を作成、監視、管理するさまざまな方法について説明します |

## <a name="miscellaneous"></a>その他
| &nbsp; | タイトル | 説明 |
| ---:|:--- |:--- |
| 74 |[Azure Data Factory - よく寄せられる質問](data-factory-faq.md) |Azure データ ファクトリについてよく寄せられる質問です。 |
| 75 |[Azure Data Factory - 関数およびシステム変数](data-factory-functions-variables.md) |Azure Data Factory の関数およびシステム変数の一覧を提供する |
| 76 |[Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) |Data Factory エンティティの名前付け規則について説明します。 |
| 77 |[Data Factory のトラブルシューティング](data-factory-troubleshoot.md) |Azure Data Factory の使用に関する問題のトラブルシューティングを行う方法について説明します。 |

<!--HONumber=Oct16_HO2-->


