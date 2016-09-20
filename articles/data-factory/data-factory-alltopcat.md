<properties
	pageTitle="Data Factory サービスに関するすべてのトピック | Microsoft Azure"
	description="http://azure.microsoft.com/documentation/articles/ に存在する Data Factory という名前の Azure サービスに関するすべてのトピックの一覧 (タイトルと説明)。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# Azure Data Factory サービスに関するすべてのトピック

このトピックでは、Azure **Data Factory** サービスに直接適用されるすべてのトピックを示します。この Web ページでは、**Ctrl + F** を使用してキーワード検索することで、現在関心があるトピックを見つけることができます。




## 新規

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 1 | [Data Factory REST API を使用した初めての Azure データ ファクトリの作成](data-factory-build-your-first-pipeline-using-rest-api.md) | このチュートリアルでは、Data Factory REST API を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 2 | [チュートリアル: コピー アクティビティがあるパイプラインを REST API で作成する](data-factory-copy-activity-tutorial-using-rest-api.md) | このチュートリアルでは、REST API を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 3 | [Data Factory コピー ウィザード](data-factory-copy-wizard.md) | Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。 |
| 4 | [Data Management Gateway](data-factory-data-management-gateway.md) | オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。Azure Data Factory で Data Management Gateway を使用してデータを移動します。 |
| 5 | [Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する ](data-factory-onprem-cassandra-connector.md) | Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する方法について説明します。 |
| 6 | [Azure Data Factory を使用して MongoDB からデータを移動する](data-factory-on-premises-mongodb-connector.md) | Azure Data Factory を使用して MongoDB データベースからデータを移動する方法を説明します。 |
| 7 | [Azure Data Factory を使用して Salesforce からデータを移動する](data-factory-salesforce-connector.md) | Azure Data Factory を使用して Salesforce からデータを移動する方法を説明します。 |


## 更新された記事

このセクションでは、最近、大幅な更新または重要な更新が行われた記事を示します。更新された各記事について、追加されたマークダウン テキストが大まかに抜粋されています。これらの記事は、**2016 年 7 月 26 日**～ **2016 年 8 月 21 日**に更新されたものです。

| &nbsp; | 記事 | 更新されたテキスト (抜粋) |
| --: | :-- | :-- |
| 8 | [Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する](data-factory-create-data-factories-programmatically.md) | **ポップアップ ダイアログ ボックスなしでのログイン** 上記のサンプル コードでは、Azure 資格情報を入力するためのダイアログ ボックスが起動します。ダイアログ ボックスを使用せずにプログラムによってサインインする必要がある場合は、Azure Resource Manager を使用したサービス プリンシパルの認証 (resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell) に関する記事をご覧ください。**例** 次に示すように、GetAuthorizationHeaderNoPopup メソッドを作成します。public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md) | **サポートされるファイル形式** コピー アクティビティでは、Azure BLOB、ファイル システム、Hadoop 分散ファイル システム (HDFS) などの、2 つのファイル ベースのデータ ストアの間でファイルをそのままコピーできます。これを行うには、入力と出力の両方のデータセット定義で format セクション (data-factory-create-datasets.md) をスキップします。これにより、シリアル化/逆シリアル化を実行することなく、データが効率的にコピーされます。また、コピー アクティビティでは、テキスト、Avro、ORC、JSON など、指定した形式でファイルの読み取りと書き込みを実行します。次に、実行可能なコピー アクティビティの例をいくつか示します。/Azure BLOB からテキスト (CSV) 形式でデータをコピーし、Azure SQL に書き込む/オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB に Avro 形式で書き込む/Azure SQL Database のデータをコピーし、オンプレミスの HDFS に ORC 形式で書き込む ** .a name="global"../a.グローバルに使用できるデータの移動**Azure Data Factory は、米国西部、米国東部、および北ヨーロッパのリージョンのみで利用できますが、コピー アクティビティを利用するサービスは、以下のリージョンと場所でグローバルに使用できます。|
| 10 | [Azure Data Factory を使用して OData ソースからデータを移動する](data-factory-odata-connector.md) | **オンプレミス OData ソースにアクセスする際に Windows 認証を使用する** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## Tutorials (チュートリアル)

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 11 | [チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md) | この Azure Data Factory のチュートリアルでは、Hadoop クラスターで Hive スクリプトを使用してデータを処理するデータ ファクトリを作成およびスケジュールする方法を示します。 |
| 12 | [チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-arm.md) | このチュートリアルでは、Azure リソース マネージャー テンプレートを使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 13 | [Azure ポータルまたは Data Factory エディターを使用した初めての Azure データ ファクトリの作成](data-factory-build-your-first-pipeline-using-editor.md) | このチュートリアルでは、Azure ポータルで Data Factory エディターを使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 14 | [Azure PowerShell を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-powershell.md) | このチュートリアルでは、Azure PowerShell を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 15 | [Microsoft Visual Studio を使用した Azure の初めての Data Factory の作成](data-factory-build-your-first-pipeline-using-vs.md) | このチュートリアルでは、Visual Studio を使用して、サンプルの Azure Data Factory パイプラインを作成します。 |
| 16 | [チュートリアル: コピー アクティビティがあるパイプラインを Data Factory Editor で作成する](data-factory-copy-activity-tutorial-using-azure-portal.md) | このチュートリアルでは、Azure ポータルで Data Factory エディターを使用して、コピー アクティビティを含む Azure Data Factory パイプラインを作成します。 |
| 17 | [チュートリアル: コピー アクティビティがあるパイプラインを Azure PowerShell で作成する](data-factory-copy-activity-tutorial-using-powershell.md) | このチュートリアルでは、Azure PowerShell を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 18 | [チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する](data-factory-copy-activity-tutorial-using-visual-studio.md) | このチュートリアルでは、Visual Studio を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |
| 19 | [Data Factory を使用した Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | このチュートリアルでは、Azure Data Factory パイプラインでコピー アクティビティを使用して、Blob Storage から SQL Database にデータをコピーする方法を示します。 |
| 20 | [チュートリアル: コピー アクティビティがあるパイプラインを Data Factory コピー ウィザードで作成する](data-factory-copy-data-wizard-tutorial.md) | このチュートリアルでは、Data Factory でサポートされているコピー ウィザードを使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。 |



## データの移動

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 21 | [Azure Data Factory を使用した Azure BLOB との間でのデータの移動](data-factory-azure-blob-connector.md) | Azure Data Factory で BLOB データをコピーする方法について説明します。サンプルを使用して、Azure Blob Storage と Azure SQL Database の間でデータをコピーする方法を示します。 |
| 22 | [Azure Data Factory を使用した Azure Data Lake Store との間でのデータの移動](data-factory-azure-datalake-connector.md) | Azure Data Factory を使用して Azure Data Lake Store に、または Azure Data Lake Store からデータを移動する方法を説明します。 |
| 23 | [Azure Data Factory を使用した DocumentDB との間でのデータの移動](data-factory-azure-documentdb-connector.md) | Azure Data Factory を使用して Azure DocumentDB コレクションに、または Azure DocumentDB コレクションからデータを移動する方法を説明します。 |
| 24 | [Azure Data Factory を使用した Azure SQL Database との間でのデータの移動](data-factory-azure-sql-connector.md) | Azure Data Factory を使用して Azure SQL Database に、または Azure SQL Database からデータを移動する方法を説明します。 |
| 25 | [Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動](data-factory-azure-sql-data-warehouse-connector.md) | Azure Data Factory を使用して Azure SQL Data Warehouse に、または Azure SQL Data Warehouse からデータを移動する方法を説明します。 |
| 26 | [Azure Data Factory を使用した Azure テーブルとの間でのデータの移動](data-factory-azure-table-connector.md) | Azure Data Factory を使用して Azure Table Storage に、または Azure Table Storage からデータを移動する方法を説明します。 |
| 27 | [コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md) | コピー アクティビティによる Azure Data Factory でのデータ移動のパフォーマンスに影響する主な要因について説明します。 |
| 28 | [コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md) | Data Factory パイプラインでのデータの移動 (クラウド ストア間、オンプレミスとクラウドの間でのデータの移行) について説明します。コピー アクティビティを使用します。 |
| 29 | [Data Management Gateway のリリース ノート](data-factory-gateway-release-notes.md) | Data Management Gateway のリリース ノート |
| 30 | [Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する](data-factory-hdfs-connector.md) | Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する方法を説明します。 |
| 31 | [新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md) | 監視と管理アプリを使用して Azure のデータ ファクトリとパイプラインを監視および管理する方法について説明します。 |
| 32 | [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) | オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。Azure Data Factory で Data Management Gateway を使用してデータを移動します。 |
| 33 | [Azure Data Factory を使用して OData ソースからデータを移動する](data-factory-odata-connector.md) | Azure Data Factory を使用して OData ソースからデータを移動する方法を説明します。 |
| 34 | [Azure Data Factory を使用して ODBC データ ストアからデータを移動する](data-factory-odbc-connector.md) | Azure Data Factory を使用して ODBC データ ストアからデータを移動する方法を説明します。 |
| 35 | [Azure Data Factory を使用して DB2 からデータを移動する](data-factory-onprem-db2-connector.md) | Azure Data Factory を使用して DB2 データベースからデータを移動する方法を説明します |
| 36 | [Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する](data-factory-onprem-file-system-connector.md) | Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する方法を説明します。 |
| 37 | [Azure Data Factory を使用して MySQL からデータを移動する](data-factory-onprem-mysql-connector.md) | Azure Data Factory を使用して MySQL データベースからデータを移動する方法を説明します。 |
| 38 | [Azure Data Factory を使用してオンプレミスの Oracle との間でデータを移動する](data-factory-onprem-oracle-connector.md) | Azure Data Factory を使用してオンプレミスの Oracle データベースとの間でデータを移動する方法を説明します。 |
| 39 | [Azure Data Factory を使用して PostgreSQL からデータを移動する](data-factory-onprem-postgresql-connector.md) | Azure Data Factory を使用して PostgreSQL Databases からデータを移動する方法を説明します。 |
| 40 | [Azure Data Factory を使用して Sybase からデータを移動する](data-factory-onprem-sybase-connector.md) | Azure Data Factory を使用して Sybase データベースからデータを移動する方法を説明します。 |
| 41 | [Azure Data Factory を使用して Teradata からデータを移動する](data-factory-onprem-teradata-connector.md) | Teradata データベースからデータを移動できる Data Factory サービスの Teradata コネクタについて学習する |
| 42 | [Azure Data Factory を使用してオンプレミスまたは IaaS (Azure VM) の SQL Server との間でデータを移動する](data-factory-sqlserver-connector.md) | Azure Data Factory を使用してオンプレミスまたは Azure VM の SQL Server データベースとの間でデータを移動する方法を説明します。 |
| 43 | [Azure Data Factory を使用して Web テーブル ソースからデータを移動する](data-factory-web-table-connector.md) | Azure Data Factory を使用してオンプレミスのテーブルからデータを移動する方法を説明します。 |



## データ操作

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 44 | [Azure Machine Learning アクティビティを使用して予測パイプラインを作成する](data-factory-azure-ml-batch-execution-activity.md) | Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します |
| 45 | [計算のリンクされたサービス](data-factory-compute-linked-services.md) | Azure Data Factory パイプラインでデータの変換/処理に使用できるコンピューティング環境について学習します。 |
| 46 | [Data Factory と Batch を使用して大規模なデータセットを処理する](data-factory-data-processing-using-batch.md) | Azure Batch の並列処理機能を使用して、Azure Data Factory パイプラインで膨大な量のデータを処理する方法について説明します。 |
| 47 | [Azure Data Factory 内のデータの変換と分析について](data-factory-data-transformation-activities.md) | Azure Data Factory でのデータ変換について説明します。Azure HDInsight クラスターまたは Azure Batch のデータを変換し、処理します。 |
| 48 | [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md) | Azure Data Factory で Hadoop ストリーミング アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hadoop ストリーミング プログラミングを実行する方法について説明します。 |
| 49 | [Hive アクティビティ](data-factory-hive-activity.md) | Azure データ ファクトリで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。 |
| 50 | [Data Factory から MapReduce プログラムを起動する](data-factory-map-reduce.md) | Azure Data Factory から Azure HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。 |
| 51 | [Pig アクティビティ](data-factory-pig-activity.md) | Azure データ ファクトリで Pig アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Pig スクリプトを実行する方法について説明します。 |
| 52 | [Data Factory から Spark プログラムを起動する](data-factory-spark.md) | MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。 |
| 53 | [SQL Server ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) | SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。 |
| 54 | [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md) | カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。 |
| 55 | [Azure Data Factory から Azure Data Lake Analytics で U-SQL スクリプトを実行する](data-factory-usql-activity.md) | Azure Data Lake Analytics コンピューティング サービスで U-SQL スクリプトを実行してデータを処理する方法について説明します。 |



## サンプル

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 56 | [Azure Data Factory - サンプル](data-factory-samples.md) | Azure Data Factory サービスに付属するサンプルについて詳細に説明します。 |



## ユース ケース

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 57 | [お客様導入事例](data-factory-customer-case-studies.md) | お客様がどのように Azure Data Factory を使用しているかについて、いくつか紹介します。 |
| 58 | [使用事例 - 顧客プロファイル](data-factory-customer-profiling-usecase.md) | Azure Data Factory を使用して、データ駆動型ワークフロー (パイプライン) を作成し、顧客のゲーム会社をプロファイリングする方法について説明します。 |
| 59 | [使用事例 - 製品に関する推奨事項](data-factory-product-reco-usecase.md) | Azure Data Factory と他のサービスを使用して実装した使用事例について説明します。 |



## 監視と管理

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 60 | [Azure Data Factory のパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) | Azure ポータルおよび Azure PowerShell を使用して、作成した Azure Data Factory とパイプラインを監視および管理する方法について説明します。 |



## SDK

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 61 | [Azure Data Factory - .NET API の変更ログ](data-factory-api-change-log.md) | Azure Data Factory.の特定のバージョンの .NET API における重大な変更、機能の追加、バグ修正などについて説明します。 |
| 62 | [Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する](data-factory-create-data-factories-programmatically.md) | Data Factory SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理する方法について説明します。 |
| 63 | [Azure Data Factory の開発者用リファレンス](data-factory-sdks.md) | Azure Data Factory を作成、監視、管理するさまざまな方法について説明します |



## その他

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 64 | [Azure Data Factory - よく寄せられる質問](data-factory-faq.md) | Azure データ ファクトリについてよく寄せられる質問です。 |
| 65 | [Azure Data Factory - 関数およびシステム変数](data-factory-functions-variables.md) | Azure Data Factory の関数およびシステム変数の一覧を提供する |
| 66 | [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) | Data Factory エンティティの名前付け規則について説明します。 |
| 67 | [Data Factory のトラブルシューティング](data-factory-troubleshoot.md) | Azure Data Factory の使用に関する問題のトラブルシューティングを行う方法について説明します。 |

<!---HONumber=AcomDC_0907_2016-->