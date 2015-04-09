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
	ms.date="03/09/2015" 
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


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## 概要
これまでのデータ統合プロジェクトは、抽出/変換/読み込み (ETL) 処理を中心として展開されてきました。ETL とは、組織内の各種データ ソースからデータを抽出し、エンタープライズ データ ウェアハウス (EDW) のターゲット スキーマに準拠するようにデータを変換して、EDW にデータを読み込む処理です。この処理の後、EDW は、BI ソリューションや分析ソリューションから唯一の情報源としてアクセスされます。 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

今日の企業を取り巻くデータ環境は、量、種類、複雑さのいずれにおいても指数関数的に増大し続けています。形式もスピードも異なる内部設置型データとクラウドで生成されたデータが混ざり合い、かつてないほど多様化しています。データ処理は、各地に分散する場所にまたがって行わなければならず、オープン ソース ソフトウェア、商用ソリューション、カスタム処理サービスが併用され、高コストで、統合や保守が困難なものとなっています。変化し続ける現代のビッグ データ環境への適用に欠かせない機敏性は、従来の EDW に現代の情報生成システムとして求められる各種機能を組み合わせることで獲得できます。  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure Data Factory** サービスは、従来の EDW と変化するデータ環境の両方に対して機能する混合プラットフォームであり、企業は利用可能なすべてのデータを活用してデータ主導の意思決定を行うことができるようになります。企業は、データの処理、保存、および移動のサービスを情報生成パイプラインとして構成して、信頼できるデータ資産を管理するプラットフォームを獲得でき、今日のデータ環境がもたらす多様性を活用できます。

Azure Data Factory サービスでは、以下を実行できます。

- **さまざまなデータ保存システムや処理システムと簡単に連携。** 
Data Factory サービスでは、オンプレミス データ (SQL Server など) と、Azure SQL データベース、Azure のテーブル、BLOB などのクラウド データ ソースの両方を移動して処理する情報生成パイプラインを作成することができます。 
- **データを信頼できる情報に変換。** 
Data Factory サービスは、Hive、Pig、および C# の処理に加えて、Hadoop (HDInsight) クラスターの自動管理、一時的な障害発生時の再試行、構成可能なタイムアウト ポリシー、アラート機能などの主要な処理機能をサポートしています。  
- **1 か所でデータ パイプラインを監視。** 
Data Factory サービスでは、データの保存、処理、移動の各サービスの全体像について、信頼性の高い情報が得られます。データ パイプライン全体の正常性を迅速に評価し、問題を特定して、必要に応じて是正処置を取ることができます。データの系列とデータ間のリレーションシップをソース全体にわたって視覚的に追跡し、ジョブの実行、システムの正常性、依存関係の履歴全体を 1 つの監視ダッシュボードから確認することもできます。
- **変換されたデータから豊富な洞察を獲得。**
Data Factory サービスでは、ビジネス インテリジェンスと分析ツール、およびその他のアプリケーションで使用できる信頼性の高いデータを生成するデータ パイプラインを作成することができます。

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## アプリケーション モデル
次の図は、Azure Data Factory サービスでサポートされているアプリケーション モデルを示しています。

![Application Model][image-data-factory-application-model]

Azure データ ファクトリには 3 つの情報生成段階があります。

- **接続と収集**。この段階では、さまざまなデータ ソースからデータがデータ ハブにインポートされます。データ ファクトリ内のパイプラインは、1 つまたは複数のアクティビティを持つことができます。データ パイプラインで 1 つまたは複数の**コピー** アクティビティを使用すると、ソース データ ストアからデータを収集し、その後の処理を行うデータ ハブ内のターゲット データ ストアに送ることができます。HDInsight クラスター (計算) および関連する Azure BLOB ストレージ (ストレージ) が一緒になって、データ ハブ、つまり HDInsight データ ハブを形成します。HDInsight データ ハブを使用するには、HDInsight クラスターでデータを処理できるように、すべてのソース データを HDInsight に関連付けられている Azure の BLOB ストアにコピーします。パイプラインは、HDInsight クラスターなどのデータ ハブ内のコンピューティング リソースで実行されます。      
- **変換と強化**。この段階では、収集されたデータが処理されます。たとえば、パイプライン内の **HDInsight アクティビティ**では、Hive や Pig のスクリプトを使用してデータを信頼性の高い情報に変換することによって、関連付けられている Azure の BLOB ストアに格納されているデータを処理できます。(図に示すように) パイプラインを連結すると、パイプラインの出力データ セットを同じデータ ハブまたは別のデータ ハブの別のパイプラインの入力データ セットにすることもできます。  
- **発行**。この段階では、BI ツールや分析ツールなどのアプリケーションで使用できるように、データが発行されます。たとえば、パイプライン内でコピー アクティビティを実行する場合であれば、変換と強化の段階で実行された処理の出力データを、ビジネス インテリジェンス ソリューションの構築の土台となるデータ ストア (例: 内部設置型 SQL Server) にコピーできます。   

<!--

開発者はデータ ファクトリを使用してパイプラインを作成できます。パイプラインとは、データの移動や処理のアクティビティの集合体であり、アクティビティとは、1 つ以上の入力データセットを受け付け、1 つ以上の出力データセットを生成する処理を指します。パイプラインは 1 回だけ実行することも、柔軟なスケジュール (1 時間ごと、毎日、毎週など) で実行することもできます。データセットとは、データの名前付きビューです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、テーブルやモデルまでさまざまです。

パイプラインは、データの移動アクティビティ (コピー アクティビティなど) から成り、組織で使用されるすべてのデータ ソース (データベース、ファイル、SaaS サービスなど) のデータをデータ ハブにインポート/エクスポートするために頻繁に使用されます。
 
データが**ハブ**に集まると、ハブのコンピューティング サービスにホストされた**パイプライン**を使用して、(BI ツール、アプリケーション、顧客などによる) 利用に適した形式にデータが変換されます。  
  
最終的に、**パイプライン**は、(図に示すように) 1 つのパイプラインの出力**データセット**が別のパイプラインの入力になるように連結できます。これにより、複雑なデータ フローを**パイプライン**に組み込んで、1 つのデータ ハブ内で実行したり、複数のハブに展開したりできます。**パイプライン**をこのように使用することで、組織は、管理が容易な単一の Data Factory という視点で、最高水準の内部設置型、クラウド、およびサービスとしてのソフトウェア (SaaS) サービスを構成するために必要な要素が揃います。
--> 

## エクスペリエンスの作成

データ ファクトリを生成または作成する方法には、以下のものがあります。

- **Azure プレビュー ポータル**。Azure プレビュー ポータルの Data Factory ブレードは、データ ファクトリ、およびリンクされたサービスを作成するための優れたユーザー インターフェイスです。**Data Factory エディター**は、ポータルの一部でもあり、これらの成果物の JSON の定義を指定することによって、リンクされたサービス、テーブル、データ セット、およびパイプラインを簡単に作成できる機能です。エディターの概要については、[Data Factory エディター][data-factory-editor]に関するページを、データ ファクトリを作成およびデプロイするためのポータルやエディターの使用例については、[Data Factory の使用][datafactory-getstarted]に関するページを参照してください。   
- **Azure PowerShell**。PowerShell ユーザーがポータル UI ではなく PowerShell の使用を希望している場合は、Azure PowerShell の一部として出荷される Azure Data Factory コマンドレットを使用してデータ ファクトリを作成し、デプロイできます。データ ファクトリの作成およびデプロイに関する単純な例については、[Azure PowerShell を使用した Azure Data Factory の作成と監視][create-data-factory-using-powershell]に関するページを、PowerShell コマンドレットの高度な使用例については、「[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial]」を参照してください。Data Factory コマンドレットの包括的なドキュメントについては、MSDN ライブラリの [Data Factory コマンドレット リファレンス][adf-powershell-reference]に関するコンテンツを参照してください。  
- **.NET クラス ライブラリ**。Data Factory .NET SDK を使用すると、プログラムでデータ ファクトリを作成できます。.NET SDK を使用したデータ ファクトリの作成のチュートリアルについては、[.NET SDK を使用したデータ ファクトリの作成、監視、および管理][create-factory-using-dotnet-sdk]に関するページを参照してください。Data Factory .NET SDK の包括的なドキュメントについては、[Data Factory クラス ライブラリ リファレンス][msdn-class-library-reference]に関するページを参照してください。  
- **REST API**。Azure Data Factory サービスで公開されている REST API を使用して、データ ファクトリを作成およびデプロイすることもできます。Data Factory REST API の包括的なドキュメントについては、[Data Factory REST API リファレンス][msdn-rest-api-reference]に関するページを参照してください。 


##用語集
このセクションでは、Azure Data Factory に関連する用語について説明します。

### データ ファクトリ
**Azure データ ファクトリ**には、Azure HDInsight などのリンクされたコンピューティング サービスを使用して、リンクされたデータ ストア (Azure ストレージ、Azure SQL データベース、内部設置型 SQL Server など) のデータを処理するパイプラインが 1 つ以上あります。Azure データ ファクトリ自体にはデータは含まれません。データは前に説明したデータ ストアに格納されます。  

### リンクされたサービス
**リンクされたサービス**とは、Azure データ ファクトリにリンクされているサービスです。リンクされたサービスは、次のいずれかです。

- Azure Storage、Azure SQL Database、内部設置型 SQL Server データベースなどの**データ ストレージ** サービス。データ ストアは、入力データ セットや出力データ セットのコンテナーです。    
- Azure HDInsight や Azure Machine Learning などの**コンピューティング** サービス。コンピューティング サービスでは、入力データを処理し、出力データを生成します。  

### データ セット
**データ セット**とは、データの名前付きビューです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、リレーショナル テーブルやモデルまでさまざまです。Data Factory **テーブル**は、スキーマを持つ四角形のデータ セットです。あるデータ ストアを参照するデータ ストア内にリンクされたサービスを作成した後に、そのデータ ストアに格納される入力データや出力データを表現するデータ セットを定義します。 


###パイプライン
Azure Data Factory の**パイプライン**は、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。たとえば、**コピー アクティビティ**は、ソース ストレージからターゲット ストレージにデータをコピーします。**HDInsight アクティビティ**は、Azure HDInsight クラスターを使用して、Hive クエリや Pig スクリプトによるデータ処理を実行します。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 

Azure Data Factory インスタンスを作成するための一般的な手順は、次のとおりです。

1. **データ ファクトリ** を作成する
2. データ ストアまたはコンピューティング サービスごとに**リンクされたサービス**を作成する
3. **入力データセットと出力データセット**を作成する
4. **パイプライン**を作成する 

###アクティビティ
パイプラインにおけるデータ処理の手順です。1 つ以上の入力データセットを受け取り、1 つ以上の出力データセットを生成します。アクティビティは実行環境 (例: Azure HDInsight クラスター) で実行され、Azure データ ファクトリと関連付けまたはリンクされているデータ ストアの読み取りや書き込みを行います。 

Azure Data Factory サービスは、パイプライン内の次のアクティビティをサポートします。 

- **コピー アクティビティ**は、データをデータ ストアから別のデータ ストアにコピーします。コピー アクティビティがサポートするデータ ストアの詳細については、「[Azure Data Factory を使用してデータをコピーする][copy-data-with-adf]」を参照してください。 
- **HDInsight アクティビティ**は、HDInsight クラスター上で Hive/Pig スクリプトまたは MapReduce プログラムを実行してデータを処理します。詳細については、[Data Factory での Pig と Hive の使用][use-pig-hive]に関するページおよび [Data Factory からの MapReduce プログラムの呼び出し][run-map-reduce]に関するページを参照してください。 
- **Azure Machine Learning のバッチ スコアリング アクティビティ**は、Azure Machine Learning のバッチ スコアリング API を呼び出します。詳細については、[Azure Data Factory および Azure Machine Learning を使用した予測パイプラインの作成][azure-ml-adf]に関するページを参照してください。 
- **ストアド プロシージャ アクティビティ**は、Azure SQL データベース内のストアド プロシージャを呼び出します。詳細については、MSDN ライブラリの[ストアド プロシージャ アクティビティ][msdn-stored-procedure-activity]に関するページを参照してください。   

###スライス
Azure データ ファクトリのテーブルは、スライスで構成されます。スライスの幅は、スケジュール (毎時/毎日) によって決まります。スケジュールが "毎時" のときは、パイプラインの開始時刻から終了時刻までに、スライスが 1 時間ごとに作成されます。たとえば、パイプラインの開始日時が 2015 年 3 月 3 日 6 時 0 分 0 秒 (午前 6 時)、終了日時が 2015 年 3 月 3 日 9 時 0 分 0 秒 (同じ日の午前 9 時) の場合、1 時間間隔で、午前 6 ～ 7 時、午前 7 ～ 8 時、午前 8 ～ 9 時の 3 つのデータ スライスが生成されます。    

スライスを使えば、特定の時間帯の全データのサブセットを利用できます (例: 午後 1 時から午後 2 時の間に生成されたスライス)。 

### スライスに対するアクティビティ実行
**実行** (アクティビティの実行) がスライスの処理単位です。再試行する場合や、エラー発生時にスライスを再実行する場合には、1 つのスライスを 1 回以上実行することもあります。スライスは、開始時刻によって識別されます。

###Data Management Gateway
Microsoft **Data Management Gateway** は、内部設置型のデータ ソースをクラウド サービスで使用できるように接続するソフトウェアです。内部設置型のデータ ソースをリンクされたサービスとして追加するには、企業環境に少なくとも 1 つのゲートウェイをインストールし、Azure Data Factory ポータルに登録する必要があります。
 
###データ ハブ
**データ ハブ**は、データのストレージおよびコンピューティング サービスの論理コンテナーです。たとえば、ストレージでは HDFS を使用する Hadoop クラスターがデータ ハブで、コンピューティング (処理) では Hive/Pig がデータ ハブです。同様に、エンタープライズ データ ウェアハウス (EDW) をデータ ハブとしてモデル化できます (ストレージとしてはデータベース、コンピューティング サービスとしてはストアド プロシージャや ETL ツール)。パイプラインは、データ ハブ内でデータ ストアを使用し、コンピューティング リソースを実行します。現時点でサポートされているのは、HDInsight ハブのみです。

データ ハブを使用して、Data Factory を論理的なグループやドメイン別のグループに分割できます。たとえば、"米国西部 Azure ハブ" を作成して、米国西部データ センター向けのリンクされたサービス (データ ストアとコンピューティング) とパイプラインを管理したり、"営業部門 EDW ハブ" を作成して、営業部門エンタープライズ データ ウェアハウスのデータの入力と処理に関連するすべてのリンクされたサービスとパイプラインを管理したりできます。

ハブの重要な特性は、パイプラインが 1 つのハブで実行されることです。つまり、パイプラインを定義する際、パイプライン内のテーブルまたはアクティビティによって参照されるすべてのリンクされたサービスには、パイプライン自体と同じハブ名を設定する必要があります。リンクされたサービスに HubName プロパティが指定されていない場合、リンクされたサービスは "Default" ハブに配置されます。

##次のステップ

1. [Data Factory の使用][datafactory-getstarted]。リンク先の記事では、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。
2. [チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial]この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する**現実のシナリオ**の実行方法について、**詳細なチュートリアル**が記載されています。
3. [Azure Data Factory - よく寄せられる質問][adf-faq]。この記事では、よく寄せられる質問と回答の一覧を示します。 
3. [Azure Data Factory で使用する一般的なシナリオ][adf-common-scenarios]。この記事では、Azure Data Factory サービスを使用するいくつかの一般的なシナリオについて説明します。


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

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

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->