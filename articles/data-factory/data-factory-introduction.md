<properties 
	pageTitle="Azure Data Factory の概要" 
	description="Azure データ ファクトリ サービスを使用してデータ処理、データ保存、データ移動のサービスを構成し、信頼済みの情報を生成するパイプラインを作成する方法について説明します。" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Azure Data Factory サービスの概要
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


**Azure Data Factory** は完全に管理されたサービスで、データの保存、データの処理、データの移動の各サービスを効率的かつスケーラブルで信頼性の高いデータ生成パイプラインとして構成します。Data Factory サービスでは、以下を実行できます。

- オンプレミス、クラウド ベース、およびインターネットのデータ ストアから取得したデータを結合、集計、および変換するデータ主導のワークフロー (パイプライン) を構築します。 
- さまざまなデータ ソースの、半構造化、非構造化、および構造化されたデータを信頼できる情報に変換します。
- ビジネス インテリジェンス (BI)、分析ツール、およびその他のアプリケーションを使用して簡単に使用できるデータを生成します。 
- 単純な JSON スクリプトを通じて、複雑なデータ処理を設定します。
- Azure プレビュー ポータルが提供する充実したビジュアル エクスペリエンスを通じて、これらのパイプラインの監視と管理をひとめで行えます。  

次のビデオは、Azure Data Factory サービスの簡単な概要を紹介します。


- [ビデオ: Azure Data Factory の概要](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## 概要
これまでのデータ統合プロジェクトは、抽出/変換/読み込み (ETL) 処理を中心として展開されてきました。ETL とは、組織内の各種データ ソースからデータを抽出し、エンタープライズ データ ウェアハウス (EDW) のターゲット スキーマに準拠するようにデータを変換して、EDW にデータを読み込む処理です。この処理の後、EDW は、BI ソリューションや分析ソリューションから唯一の情報源としてアクセスされます。

![従来の ETL][image-data-factory-introduction-traditional-ETL]

今日の企業を取り巻くデータ環境は、量、種類、複雑さのいずれにおいても指数関数的に増大し続けています。形式もスピードも異なる内部設置型データとクラウドで生成されたデータが混ざり合い、かつてないほど多様化しています。データ処理は、各地に分散する場所にまたがって行わなければならず、オープン ソース ソフトウェア、商用ソリューション、カスタム処理サービスが併用され、高コストで、統合や保守が困難なものとなっています。変化し続ける現代のビッグ データ環境への適用に欠かせない機敏性は、従来の EDW に現代の情報生成システムとして求められる各種機能を組み合わせることで獲得できます。

![今日のさまざまな処理のランドスケープ][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure Data Factory** サービスは、従来の EDW と変化するデータ環境の両方に対して機能する構成プラットフォームであり、企業は利用可能なすべてのデータを活用してデータ主導の意思決定を行えるようになります。企業は、データの処理、保存、および移動のサービスを情報生成パイプラインとして構成して、信頼できるデータ資産を管理するプラットフォームを獲得でき、今日のデータ環境がもたらす多様性を活用できます。

Azure Data Factory サービスでは、以下を実行できます。

- **さまざまなデータ保存システムや処理システムと簡単に連携。** Data Factory サービスでは、オンプレミス データ (SQL Server など) と、Azure SQL データベース、Azure のテーブル、BLOB などのクラウド データ ソースの両方を移動して処理する情報生成パイプラインを作成することができます。 
- **データを信頼できる情報に変換。** Data Factory サービスは、Hive、Pig、および C# の処理に加えて、Hadoop (HDInsight) クラスターの自動管理、一時的な障害発生時の再試行、構成可能なタイムアウト ポリシー、アラート機能などの主要な処理機能をサポートしています。  
- **1 か所でデータ パイプラインを監視。** Data Factory サービスでは、データの保存、処理、移動の各サービスの全体像について、信頼性の高い情報が得られます。データ パイプライン全体の正常性を迅速に評価し、問題を特定して、必要に応じて是正処置を取ることができます。データの系列とデータ間のリレーションシップをソース全体にわたって視覚的に追跡し、ジョブの実行、システムの正常性、依存関係の履歴全体を 1 つの監視ダッシュボードから確認することもできます。
- **変換されたデータから豊富な洞察を獲得。**Data Factory サービスでは、ビジネス インテリジェンスと分析ツール、およびその他のアプリケーションで使用できる信頼性の高いデータを生成するデータ パイプラインを作成することができます。

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## アプリケーション モデル
次の図は、Azure Data Factory サービスでサポートされているアプリケーション モデルを示しています。

![アプリケーション モデル][image-data-factory-application-model]

Azure データ ファクトリには 3 つの情報生成段階があります。

- **接続と収集**。この段階では、さまざまなデータ ソースからデータがデータ ハブにインポートされます。データ ファクトリ内のパイプラインは、1 つまたは複数のアクティビティを持つことができます。データ パイプラインで 1 つまたは複数の**コピー** アクティビティを使用すると、ソース データ ストアからデータを収集し、その後の処理を行うデータ ハブ内のターゲット データ ストアに送ることができます。HDInsight クラスター (計算) および関連する Azure BLOB ストレージ (ストレージ) が一緒になって、データ ハブ、つまり HDInsight データ ハブを形成します。HDInsight データ ハブを使用するには、HDInsight クラスターでデータを処理できるように、すべてのソース データを HDInsight に関連付けられている Azure の BLOB ストアにコピーします。パイプラインは、HDInsight クラスターなどのデータ ハブ内のコンピューティング リソースで実行されます。      
- **変換と強化。**この段階では、収集されたデータが処理されます。たとえば、パイプライン内の **HDInsight アクティビティ**では、Hive や Pig のスクリプトを使用してデータを信頼性の高い情報に変換することによって、関連付けられている Azure の BLOB ストアに格納されているデータを処理できます。(図に示すように) パイプラインを連結すると、パイプラインの出力データ セットを同じデータ ハブまたは別のデータ ハブの別のパイプラインの入力データ セットにすることもできます。  
- **発行**。この段階では、BI ツールや分析ツールなどのアプリケーションで使用できるように、データが発行されます。たとえば、パイプライン内でコピー アクティビティを実行する場合であれば、変換と強化の段階で実行された処理の出力データを、ビジネス インテリジェンス ソリューションの構築の土台となるデータ ストア (例: 内部設置型 SQL Server) にコピーできます。   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##次のステップ
1. [Data Factory を使ってみましょう][datafactory-getstarted]。リンク先の記事では、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。
2. [チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial]この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する**現実のシナリオ**の実行方法について、**詳細なチュートリアル**が記載されています。

## 関連項目
- [Data Factory - 用語][adf-terminology]。この記事で、Azure Data Factory サービスで Data Factory を作成する際に使われる用語を紹介します。 
- [Data Factory - よく寄せられる質問][adf-faq]。この記事では、よく寄せられる質問と回答の一覧を示します。
- [Azure Data Factory を使用する一般的なシナリオ][adf-common-scenarios]。この記事では、Azure Data Factory サービスを使用するいくつかの一般的なシナリオについて説明します。 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=July15_HO3-->