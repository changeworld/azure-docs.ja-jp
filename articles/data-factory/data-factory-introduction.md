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
	ms.topic="get-started-article" 
	ms.date="11/18/2015" 
	ms.author="spelluru"/>

# Azure Data Factory サービスの概要

## 概要
Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。原材料を機械で加工して最終製品を作成する工場と同じように、Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。

Data Factory は、オンプレミスとクラウドのデータ ソースおよび SaaS で動作し、データの取り込み、準備、変換、分析、および発行を行います。Data Factory を使えば、各種のサービスを管理されたデータ フロー パイプラインにまとめ上げることによって、ビッグ データのコンピューティング ニーズに応える [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) や [Azure Batch](http://azure.microsoft.com/documentation/services/batch/) のほか、分析ソリューションを運用可能にするための [Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/) などを使ったデータの変換を実現できます。単なる表形式の監視ビューではなく、Data Factory のリッチな視覚化機能を使用してデータ パイプライン間の系列と依存関係をすばやく表示します。1 つの統一されたビューからすべてのデータ フロー パイプラインを監視し、問題を簡単に特定して監視アラートを設定します。

![概要](./media/data-factory-introduction/data-factory-overview.png)

**図 1** 多くのさまざまなオンプレミス データ ソースからデータを収集し、データを取り込んで準備し、広範な変換でデータを整理して分析し、消費用にすぐ使用できるデータを発行します。

必要があればいつでも Data Factory を使用して、異なる形状とサイズのデータを収集し、変換し、発行して深い洞察を抽出することのすべてを、信頼できるスケジュールで実行できます。Data Factory を使えば、さまざまな業界の分析とパイプラインのニーズに対応し、数多くのシナリオにも対応する、可用性の高いデータ フロー パイプラインを作成できます。たとえば、オンラインの小売業者であれば、Data Factory を使って顧客の閲覧行動に基づいて顧客一人一人に合わせた[お勧めの商品](data-factory-product-reco-usecase.md)を生成することが考えられます。また、ゲーム スタジオが自らの[マーケティング キャンペーンの効果](data-factory-customer-profiling-usecase.md)の測定に使用することもできるでしょう。お客様が Data Factory を使用する方法と理由については、[お客様導入事例](data-factory-customer-case-studies.md)を直接ご覧ください。

> [AZURE.VIDEO azure-data-factory-overview]

## 主要な概念

Azure Data Factory には、入力および出力データ、処理イベント、および目的のデータ フローの実行に必要なスケジュールとリソースを定義するために連携するいくつかの重要なエンティティがあります。

![主要な概念](./media/data-factory-introduction/key-concepts.png)

**図 2.** データセット、アクティビティ、パイプライン、リンクされたサービスの間の関係


### アクティビティ
アクティビティは、データに対して実行するアクションを定義します。各アクティビティは、入力として 0 個以上の[データセット](data-factory-create-datasets.md)を受け取り、出力として 1 つまたは複数のデータセットを生成します。アクティビティは、Azure Data Factory でのオーケストレーションの単位です。たとえば、[コピー アクティビティ](data-factory-data-movement-activities.md)を使用して、データセット間のデータのコピーを調整できます。同様に、Azure HDInsight クラスターに対して Hive クエリを実行する [Hive アクティビティ](data-factory-data-transformation-activities.md)を使用して、データを変換および分析できます。Azure Data Factory には、データ変換、分析、データ移動のための広範なアクティビティがあります。

### パイプライン
[パイプライン](data-factory-create-pipelines.md)は、アクティビティの論理的なグループです。パイプラインは、まとまってタスクを実行するユニットにアクティビティをグループ化するために使用されます。たとえば、複数の変換アクティビティのシーケンスがログ ファイル データのクレンジングに必要な場合があります。このシーケンスには、調整および自動化が必要な複雑なスケジュールと依存関係があります。これらのアクティビティのすべてを "CleanLogFiles" という名前の 1 つのパイプラインにグループ化できます。個々のアクティビティを個別に管理するのではなく、"CleanLogFiles" を 1 つの単位としてデプロイ、スケジューリング、削除できます。

### データセット
[データセット](data-factory-create-datasets.md)は、アクティビティの入力または出力として使用するデータへの名前付きの参照/ポインターです。データセットは、テーブル、ファイル、フォルダー、ドキュメントなどのさまざまなデータ ストア内のデータ構造を示します。

### リンクされたサービス
リンクされたサービスは、Data Factory が外部リソースに接続するために必要な情報を定義します。Data Factory ではリンクされたサービスは 2 つの目的に使用されます。

- オンプレミスの SQL Server、Oracle DB、ファイル共有、Azure BLOB ストレージ アカウント、その他のデータ ストアを表すため。前述のように、データセットは、リンクされたサービスによって Data Factory に接続されるデータ ストア内の構造を表します。
- アクティビティの実行をホストできるコンピューティング リソースを表すため。たとえば、“HDInsightHive Activity” は HDInsight Hadoop クラスターで実行します。

データセット、アクティビティ、パイプライン、およびリンクされたサービスという 4 つの簡単な概念を理解すれば、Azure Data Factory を使用できます。 何もない状態から[最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md)ことも、[Data Factory のサンプル](data-factory-samples.md)を示した記事の説明に従って既成のサンプルをデプロイすることもできます。

<!---HONumber=Nov15_HO4-->